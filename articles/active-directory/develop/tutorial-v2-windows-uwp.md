---
title: 'Zelfstudie: Een Universal Windows Platform-app (UWP) maken die gebruikmaakt van het Microsoft-identiteitsplatform voor verificatie | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie bouwt u een UWP-app die gebruikmaakt van het Microsoft-identiteitsplatform voor het aanmelden van gebruikers en krijgt u een toegangstoken de Microsoft Graph API namens hen aan te roepen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: dce2cd0d77ff0a98d4d68e1c99edb472e61ce8a5
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509458"
---
# <a name="tutorial-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Zelfstudie: De Microsoft Graph-API aanroepen vanuit de Universeel Windows-platformtoepasing (UWP)

In deze zelfstudie bouwt u een systeemeigen Universal Windows Platform (UWP) die gebruikers aanmeldt en een toegangstoken ophaalt om de Microsoft Graph API aan te roepen. 

Aan het einde van deze handleiding roept uw toepassing een beveiligde API aan met persoonlijke accounts. Voorbeelden zijn outlook.com, live.com, enzovoort. Uw toepassing roept ook werk- en schoolaccounts aan van een bedrijf of organisatie met Azure Active Directory (Azure AD).

In deze zelfstudie:

> [!div class="checklist"]
> * Een *Universal Windows Platform (UWP)* -project maken in Visual Studio
> * De app registreren in de Azure Portal
> * Code toevoegen voor de ondersteuning van het aan- en afmelden van gebruikers
> * Code toevoegen om de Microsoft Graph API aan te roepen
> * De app testen

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) waarbij de werkbelasting [Universeel Windows-platformontwikkeling](/windows/uwp/get-started/get-set-up) is ingeschakeld

## <a name="how-this-guide-works"></a>De werking van deze handleiding

![Er wordt getoond hoe de voorbeeld-app werkt die wordt gegenereerd in deze zelfstudie](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

In deze handleiding wordt een voorbeeld-UWP-toepassing gemaakt die een query uitvoert op de Microsoft Graph API. Voor dit scenario wordt een token toegevoegd aan HTTP-aanvragen via de autorisatie-header. Het verkrijgen en vernieuwen van tokens wordt verwerkt in Microsoft Authentication Library.

## <a name="nuget-packages"></a>NuGet-pakketten

Deze handleiding maakt gebruik van het volgende NuGet-pakket:

|Bibliotheek|Beschrijving|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library|
|[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph-clientbibliotheek|

## <a name="set-up-your-project"></a>Uw project instellen

In deze sectie vindt u stapsgewijze instructies voor het integreren van een Windows Desktop .NET-toepassing (XAML) met aanmelding bij Microsoft. Vervolgens kan de toepassing query's uitvoeren op Web-API's waarvoor een token is vereist, zoals de Microsoft Graph API.

In deze handleiding wordt een toepassing gemaakt waarin een knop voor het uitvoeren van een query op de Microsoft Graph API en een knop voor het afmelden worden weergegeven. Er worden ook tekstvakken weergegeven met de resultaten van de aanroepen.

> [!NOTE]
> Wilt u het Visual Studio-project uit dit voorbeeld downloaden in plaats van het te maken? [Download een project](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip), en ga door naar de stap [toepassingsregistratie](#register-your-application "stap voor toepassingsregistratie") om het codevoorbeeld te configureren voordat u het uitvoert.

### <a name="create-your-application"></a>Uw toepassing maken

1. Open Visual Studio en selecteer **Een nieuw project maken**.
1. Kies in het venster **Een nieuw project maken** de optie **Lege app (Universal Windows)** voor C# en selecteer **Volgende**.
1. In **Uw nieuwe project configureren**, geeft u de app een naam en selecteert u **Maken**.
1. Als u hierom wordt gevraagd, selecteert u in **Nieuw Universeel Windows-platformproject** een willekeurige versie voor de **minimum**- en **doel** versies en selecteert u **OK**.

   ![Minimum- en doelversies](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>Microsoft Authentication Library toevoegen aan uw project

1. Selecteer in Visual Studio **Tools** > **NuGet Package Manager** > **Package Manager Console**.
1. Kopieer en plak de volgende opdrachten in het venster **Pakketbeheerconsole**:

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > Met de eerste opdracht wordt [Microsoft Authentication Library (MSAL.NET)](https://aka.ms/msal-net) geïnstalleerd. MSAL.NET verkrijgt, bewaart en vernieuwt gebruikerstokens die toegang hebben tot API's die zijn beveiligd met het Microsoft identity platform. Met de tweede opdracht wordt de [Microsoft Graph .NET-clientbibliotheek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) geïnstalleerd om aanvragen voor Microsoft Graph te verifiëren en aanroepen naar de service te verzenden.

### <a name="create-your-applications-ui"></a>De gebruikersinterface voor uw toepassing maken

Visual Studio maakt *MainPage.xaml* als onderdeel van de projectsjabloon. Open dit bestand en vervang het knooppunt **Grid** (Raster) van uw toepassing door de volgende code:

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Microsoft Authentication Library gebruiken voor het ophalen van een token voor de Microsoft Graph API

In deze sectie wordt uitgelegd hoe u met Microsoft Authentication Library een token voor de Microsoft Graph API kunt ophalen. Breng wijzigingen aan in het bestand *MainPage.xaml.cs*.

1. Voeg de volgende verwijzingen toe in *MainPage.xaml.cs*:

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. Vervang de klasse `MainPage` door de volgende code:

    ```csharp
    public sealed partial class MainPage : Page
    {

        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen<a name="more-information"></a>

De methode `AcquireTokenInteractive` resulteert in een venster waarin de gebruiker wordt gevraagd zich aan te melden. De eerste keer moeten gebruikers zich in toepassingen doorgaans interactief aanmelden om toegang te krijgen tot een beveiligde resource. Ze moeten zich mogelijk ook aanmelden wanneer een stille bewerking voor het verkrijgen van een token mislukt. Dit moet bijvoorbeeld wanneer het wachtwoord van een gebruiker is verlopen.

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Met de methode `AcquireTokenSilent` wordt het verkrijgen en vernieuwen van tokens verwerkt zonder tussenkomst van de gebruiker. Nadat `AcquireTokenInteractive` voor de eerste keer is uitgevoerd en de gebruiker om referenties is gevraagd, gebruikt u de methode `AcquireTokenSilent` om tokens aan te vragen voor latere aanroepen. Met deze methode worden tokens op de achtergrond verkregen. Het opslaan in de cache en vernieuwen van tokens wordt verwerkt in Microsoft Authentication Library.

Uiteindelijk mislukt de methode `AcquireTokenSilent`. De mislukking kan het gevolg zijn van een gebruiker die zich heeft afgemeld of die het wachtwoord op een ander apparaat heeft gewijzigd. Als in Microsoft Authentication Library wordt gedetecteerd dat een interactieve actie vereist is, wordt een `MsalUiRequiredException`-uitzondering gegenereerd. Uw toepassing kan deze uitzondering op twee manieren afhandelen:

* Uw toepassing roept direct `AcquireTokenInteractive` aan. Met deze aanroep wordt de gebruiker gevraagd zich aan te melden. Normaal gesproken gebruikt u deze methode voor onlinetoepassingen waarbij er geen offline-inhoud beschikbaar is voor de gebruiker. Het voorbeeld dat door deze begeleide installatie wordt gegenereerd, volgt dit patroon. De eerste keer dat u het voorbeeld uitvoert, ziet u hoe dit werkt.

   Omdat geen enkele gebruiker de toepassing heeft gebruikt, bevat `accounts.FirstOrDefault()` een null-waarde en wordt een `MsalUiRequiredException`-uitzondering gegenereerd.

   De code in het voorbeeld behandelt vervolgens de uitzondering door `AcquireTokenInteractive` aan te roepen. Met deze aanroep wordt de gebruiker gevraagd zich aan te melden.

* Uw toepassing bevat een visuele indicatie voor gebruikers die zich moeten aanmelden. Vervolgens kunnen ze het juiste tijdstip selecteren om zich aan te melden. De toepassing kan later opnieuw proberen om `AcquireTokenSilent` uit te voeren. Gebruik deze aanpak wanneer gebruikers de functionaliteit van andere toepassingen zonder onderbreking kunnen gebruiken. Een voorbeeld hiervan is wanneer er offline-inhoud beschikbaar is in de toepassing. In dit geval kunnen gebruikers beslissen wanneer ze zich willen aanmelden. De toepassing kan opnieuw proberen `AcquireTokenSilent` uit te voeren als het netwerk tijdelijk niet beschikbaar was.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>De Microsoft Graph-serviceclient instantiëren door het token te verkrijgen via de methode SignInUserAndGetTokenUsingMSAL

Voeg de volgende nieuwe methode toe aan *MainPage.xaml.cs*:

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Meer informatie over het maken van een REST-aanroep voor een beveiligde API

In deze voorbeeldtoepassing maakt de methode `GetGraphServiceClient` een instantie van `GraphServiceClient` met behulp van een toegangstoken. Vervolgens wordt `GraphServiceClient` gebruikt om de profielgegevens van de gebruiker op te halen van het **me**-eindpunt.

### <a name="add-a-method-to-sign-out-the-user"></a>Een methode voor het afmelden van de gebruiker toevoegen

Als u de gebruiker wilt afmelden, voegt u de volgende methode toe aan *MainPage.xaml.cs*:

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

> [!NOTE]
> MSAL.NET maakt gebruik van asynchrone methoden om tokens te verkrijgen of accounts te bewerken. U moet de gebruikersinterfaceacties ondersteunen in de gebruikersinterfacethread. Dit is de reden voor het aanroepen van `Dispatcher.RunAsync` en de voorzorgsmaatregelen voor het aanroepen van `ConfigureAwait(false)`.

#### <a name="more-information-about-signing-out"></a>Meer informatie over afmelden<a name="more-information-on-sign-out"></a>

Met de methode `SignOutButton_Click` wordt de gebruiker uit de Microsoft Authentication Library-gebruikerscache verwijderd. Met deze methode wordt Microsoft Authentication Library in feite verteld de huidige gebruiker te vergeten. Een toekomstige aanvraag voor het verkrijgen van een token slaagt alleen als deze interactief is.

De toepassing in dit voorbeeld ondersteunt één gebruiker. Microsoft Authentication Library ondersteunt scenario's waarbij de gebruiker zich kan aanmelden bij meer dan één account. Een voorbeeld hiervan is een e-mailtoepassing waarbij een gebruiker meerdere accounts heeft.

### <a name="display-basic-token-information"></a>Basisinformatie over het token weergeven

Voeg de volgende methode toe aan *MainPage.xaml.cs* om basisinformatie over het token weer te geven:

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>Meer informatie<a name="more-information-1"></a>

id-tokens die zijn verkregen met behulp van **OpenID Connect** bevatten ook een kleine subset van informatie over de gebruiker. Met `DisplayBasicTokenInfo` wordt basisinformatie in het token weergegeven. Deze informatie omvat de weergavenaam en id van de gebruiker. Het omvat ook de vervaldatum van het token en de tekenreeks die het toegangstoken zelf vertegenwoordigt. Als u meermaals de knop **Microsoft Graph API aanroepen** selecteert, ziet u dat hetzelfde token opnieuw is gebruikt voor latere aanvragen. U kunt ook zien tot wanneer de vervaldatum is verlengd wanneer in Microsoft Authentication Library wordt vastgesteld dat het token moet worden vernieuwd.

### <a name="display-message"></a>Bericht weergeven

Voeg de volgende nieuwe methode toe aan *MainPage.xaml.cs*:

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>Uw toepassing registreren

U moet uw toepassing nu registreren:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatiemap (alle Azure AD-mappen - meerdere tenants) en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox).** 
1. Selecteer **Registreren**.
1. Zoek en kopieer de waarde van de **toepassings-id (client-id)** op de overzichtspagina. Ga terug naar Visual Studio, open *MainPage.xaml.cs* en vervang de waarde van `ClientId` door deze waarde.

Verificatie configureren voor uw toepassing:

1. Selecteer als u weer in de [Azure-portal](https://portal.azure.com) bent, onder **Beheren**, achtereenvolgens **Verificatie** > **Een platform toevoegen**, en selecteer vervolgens **Mobiele toepassingen en desktoptoepassingen**.
1. Selecteer **https://login.microsoftonline.com/common/oauth2/nativeclient** in de sectie **Omleidings-URI's**.
1. Selecteer **Configureren**.

API-machtigingen voor uw toepassing configureren:

1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**.
1. Selecteer **Microsoft Graph**.
1. Selecteer **Gedelegeerde machtigingen**, zoek naar *User.Read* en controleer of **User.Read** is geselecteerd.
1. Als u wijzigingen hebt aangebracht, selecteert u **Machtigingen toevoegen** om ze op te slaan.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>Geïntegreerde verificatie inschakelen voor federatieve domeinen (optioneel)

Als u geïntegreerde Windows-verificatie wilt inschakelen wanneer deze wordt gebruikt in combinatie met een federatief Azure AD-domein, moet het manifest van de toepassing extra mogelijkheden inschakelen. Ga terug naar uw toepassing in Visual Studio.

1. Open *Package.appxmanifest*.
1. Selecteer **Mogelijkheden** en schakel de volgende instellingen in:

   * **Ondernemingsverificatie**
   * **Particuliere netwerken (client en server)**
   * **Gedeelde gebruikerscertificaten**

> [!IMPORTANT]
> [Geïntegreerde Windows-verificatie](https://aka.ms/msal-net-iwa) is niet standaard geconfigureerd voor dit voorbeeld. Voor toepassingen die `Enterprise Authentication`- of `Shared User Certificates`-mogelijkheden aanvragen, is een hoger verificatieniveau van de Windows Store vereist. Bovendien willen niet alle ontwikkelaars het hogere verificatieniveau uitvoeren. Schakel deze instelling alleen in als u geïntegreerde Windows-verificatie met een federatief Azure AD-domein nodig hebt.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>Alternatieve methode voor het gebruik van WithDefaultRedirectURI()

In het huidige voorbeeld wordt de methode `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` gebruikt. Voer de volgende stappen uit als u `WithDefaultRedirectURI()` wilt gebruiken:

1. In *MainPage.XAML.cs* vervangt u `WithRedirectUri` door `WithDefaultRedirectUri`:

   **Huidige code**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **Bijgewerkte code**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  Zoek de callback-URI voor uw app door het veld `redirectURI` toe te voegen aan *MainPage.xaml.cs* en er een onderbrekingspunt voor in te stellen:

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }

    ```

    Voer de app uit en kopieer de waarde van `redirectUri` wanneer het onderbrekingspunt wordt bereikt. De waarde moet er ongeveer als volgt uitzien: `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    U kunt de regel code vervolgens verwijderen, omdat deze slechts eenmaal is vereist, om de waarde op te halen.

3. Voeg in de app-registratieportal de geretourneerde waarde toe in **RedirectUri** in het deelvenster **Verificatie**.

## <a name="test-your-code"></a>Uw code testen

Als u uw toepassing wilt testen, selecteert u de **F5**-toets om uw project uit te voeren in Visual Studio. Het hoofdvenster wordt weergegeven:

![Gebruikersinterface van de toepassing](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

Wanneer u klaar bent om te testen, selecteert u **Microsoft Graph API aanroepen**. Gebruik vervolgens een Azure AD-organisatieaccount of een Microsoft-account, zoals live.com of outlook.com, om u aan te melden. De eerste keer dat een gebruiker deze test uitvoert, wordt in de toepassing een venster weergegeven waarin de gebruiker wordt gevraagd zich aan te melden.

### <a name="consent"></a>Toestemming

De eerste keer dat u zich bij uw toepassing aanmeldt, wordt er een toestemmingsscherm weergegeven, zoals in de volgende afbeelding. Selecteer **Ja** om expliciet toestemming te geven voor toegang:

![Toegangsbevestigingsscherm](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>Verwachte resultaten

U ziet de gebruikersprofielgegevens die worden geretourneerd door de Microsoft Graph API-aanroep op het scherm met de **resultaten van de API-aanroep**:

![Scherm met de resultaten van de API-aanroep](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

U ziet ook basisinformatie over het token dat is verkregen via `AcquireTokenInteractive` of `AcquireTokenSilent` in het vak **Token Info** (Tokengegevens):

|Eigenschap  |Indeling  |Beschrijving |
|---------|---------|---------|
|`Username` |`user@domain.com` |De gebruikersnaam waarmee de gebruiker wordt geïdentificeerd.|
|`Token Expires` |`DateTime` |Het tijdstip waarop het token verloopt. Microsoft Authentication Library verlengt de vervaldatum door het token zo nodig te vernieuwen.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>Meer informatie over bereiken en gedelegeerde machtigingen

De Microsoft Graph API vereist het bereik `user.read` om het profiel van een gebruiker te lezen. Dit bereik wordt standaard toegevoegd in elke toepassing die is geregistreerd in de toepassingsregistratieportal. Overige API's voor Microsoft Graph en aangepaste API's voor uw back-endserver vereisen mogelijk aanvullende bereiken. De Microsoft Graph API vereist bijvoorbeeld het bereik `Calendars.Read` om te luisteren naar de agenda van de gebruiker.

Als u toegang wilt krijgen tot de agenda van de gebruiker in de context van een toepassing, voegt u de gedelegeerde toestemming `Calendars.Read` toe aan de toepassingsregistratiegegevens. Voeg vervolgens het bereik `Calendars.Read` toe aan de aanroep `acquireTokenSilent`.

> [!NOTE]
> Gebruikers worden mogelijk gevraagd om aanvullende machtigingen te geven naarmate u het aantal bereiken verhoogt.

## <a name="known-issues"></a>Bekende problemen

### <a name="issue-1"></a>Probleem 1

Er wordt een van de volgende foutberichten weergegeven wanneer u zich aanmeldt bij uw toepassing in een federatief Azure AD-domein:

* Er is geen geldig clientcertificaat gevonden in de aanvraag.
* Er zijn geen geldige certificaten gevonden in het certificaatarchief van de gebruiker.
* Probeer het opnieuw met een andere verificatiemethode.

**Oorzaak:** De mogelijkheden voor ondernemingen en certificaten zijn niet ingeschakeld.

**Oplossing:** Volg de stappen in [Geïntegreerde verificatie inschakelen voor federatieve domeinen (optioneel)](#enable-integrated-authentication-on-federated-domains-optional).

### <a name="issue-2"></a>Probleem 2

U schakelt [geïntegreerde verificatie voor federatieve domeinen](#enable-integrated-authentication-on-federated-domains-optional) in en probeert Windows Hello te gebruiken op een Windows 10-computer om u aan te melden bij een omgeving waarvoor meervoudige verificatie is geconfigureerd. De lijst met certificaten wordt weergegeven. Als u ervoor kiest om uw pincode te gebruiken, wordt het venster voor de pincode nooit weergegeven.

**Oorzaak:** Dit probleem is een bekende beperking van de webverificatiebroker in UWP-toepassingen die worden uitgevoerd op Windows 10-desktops. Op Windows 10 Mobile werkt het wel.

**Tijdelijke oplossing:** Selecteer **Aanmelden met andere opties**. Selecteer vervolgens **Aanmelden met een gebruikersnaam en wachtwoord**. Selecteer **Uw wachtwoord opgeven**. Ga vervolgens door met het verificatieproces voor de telefoon.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de Microsoft Authentication Library (MSAL) voor autorisatie en verificatie in .NET-toepassingen:

> [!div class="nextstepaction"]
> [Overzicht van de Microsoft Authentication Library (MSAL)](msal-overview.md)
