---
title: 'Quickstart: Token ophalen en Microsoft Graph aanroepen in een console-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe een voorbeeld-app voor .NET Core de stroom van clientreferenties kan gebruiken om een token op te halen en Microsoft Graph aan te roepen.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: a31bf345f523eea940be5d56495890e8ab5c6dbd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861639"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Quickstart: Een token op halen en de Microsoft Graph-API aanroepen met behulp van de identiteit van een console-app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe u met een .NET Core-consoletoepassing een toegangstoken kunt verkrijgen om de Microsoft Graph API aan te roepen en een [lijst met gebruikers](/graph/api/user-list) weer te geven in de map. Het codevoorbeeld laat ook zien hoe een taak of windows-service kan worden uitgevoerd met een toepassings-id in plaats van de identiteit van een gebruiker. De voorbeeldconsoletoepassing in deze quickstart is ook een daemontoepassing, dus het is een vertrouwelijke clienttoepassing.

> [!div renderon="docs"]
> In het volgende diagram ziet u hoe de voorbeeld-app werkt:
>
> ![Diagram waarin wordt getoond hoe de voorbeeld-app werkt die is gegenereerd in deze snelstart.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Vereisten

Deze quickstart vereist [.NET Core 3.1 SDK,](https://dotnet.microsoft.com/download) maar werkt ook met .NET 5.0 SDK.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>De app registreren en downloaden

> [!div renderon="docs" class="sxs-lookup"]
>
> U hebt twee opties om te beginnen met het bouwen van uw toepassing: automatische of handmatige configuratie.
>
> ### <a name="automatic-configuration"></a>Automatische configuratie
>
> Als u uw app wilt registreren en automatisch wilt configureren en vervolgens het codevoorbeeld wilt downloaden, volgt u deze stappen:
>
> 1. Ga naar de <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal voor app-registratie.</a>
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met één klik te downloaden en automatisch te configureren.
>
> ### <a name="manual-configuration"></a>Handmatige configuratie
>
> Als u uw toepassing en codevoorbeeld handmatig wilt configureren, gebruikt u de volgende procedures.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal</span></a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Map en** abonnement in het bovenste menu om de tenant te selecteren waarin u de toepassing :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer **bij** Naam een naam in voor uw toepassing. Voer bijvoorbeeld **Daemon-console in.** Gebruikers van uw app zien deze naam en u kunt deze later wijzigen.
> 1. Selecteer **Registreren** om de toepassing te maken.
> 1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
> 1. Selecteer onder **Clientgeheimen** de optie **Nieuw clientgeheim**. Voer een naam in en selecteer vervolgens **Toevoegen**. Noteer de waarde voor het geheim op een veilige locatie, voor gebruik in een latere stap.
> 1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**. Selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer onder **het** knooppunt Gebruiker **de optie User.Read.All** en selecteer **vervolgens Machtigingen toevoegen.**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Uw snelstart-app downloaden en configureren
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Om het codevoorbeeld in deze quickstart te laten werken, maakt u een clientgeheim en voegt u de machtiging **User.Read.All** van de Graph API van de Graph API toe.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-netcore-daemon/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-visual-studio-project"></a>Stap 2: uw Visual Studio-project downloaden

> [!div renderon="docs"]
> [Download het Visual Studio-project](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> U kunt het opgegeven project uitvoeren in Visual Studio of Visual Studio voor Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Voer het project uit met behulp Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Stap 3: uw Visual Studio-project configureren
>
> 1. Extraheerde het ZIP-bestand naar een lokale map die zich dicht bij de hoofdmap van de schijf. Extraheren bijvoorbeeld naar *C:\Azure-Samples.*
>
>    We raden u aan het archief uit te extraheren naar een map in de buurt van de hoofdmap van uw station om fouten te voorkomen die worden veroorzaakt door beperkingen voor de padlengte in Windows.
>
> 1. Open de oplossing in Visual Studio: *1-Call-MSGraph\daemon-console.sln* (optioneel).
> 1. Vervang *appsettings.jsin op* de waarden van , en `Tenant` `ClientId` `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    In die code:
>    - `Enter_the_Application_Id_Here` is de toepassings-id (client-id) voor de toepassing die u hebt geregistreerd.
        Als u de waarden voor de toepassings-id (client-id) en  de map-id (tenant-id) wilt zoeken, gaat u naar de pagina Overzicht van de app in de Azure Portal.
>    - Vervang `Enter_the_Tenant_Id_Here` door de tenant-id of tenantnaam (bijvoorbeeld `contoso.microsoft.com` ).
>    - Vervang `Enter_the_Client_Secret_Here` door het clientgeheim dat u in stap 1 hebt gemaakt.
    Als u een nieuwe sleutel wilt genereren, gaat u naar **de pagina Certificaten & geheimen.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Stap 3: toestemming van de beheerder

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Stap 4: toestemming van de beheerder

Als u de toepassing op dit moment probeert uit te voeren, ontvangt u de fout *HTTP 403 - Verboden:* Onvoldoende bevoegdheden om de bewerking te voltooien. Deze fout t opgetreden omdat voor elke alleen-app-machtiging een globale beheerder van uw directory is vereist om toestemming te geven aan uw toepassing. Selecteer een van de volgende opties, afhankelijk van uw rol.

##### <a name="global-tenant-administrator"></a>Globale tenantbeheerder

> [!div renderon="docs"]
> Als u een globale tenantbeheerder bent, gaat u naar **Bedrijfstoepassingen** in de Azure Portal. Selecteer uw app-registratie en selecteer **Machtigingen** in de **sectie** Beveiliging van het linkerdeelvenster. Selecteer vervolgens de grote knop met het label Beheerder toestemming verlenen voor **{Tenant Name}** (waarbij **{Tenant Name}** de naam van uw directory is).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de **pagina API-machtigingen** en selecteert u **Beheerder toestemming verlenen voor Enter_the_Tenant_Name_Here.**
> > [!div id="apipermissionspage"]
> > [Ga naar de pagina API-machtigingen]()

##### <a name="standard-user"></a>Standaardgebruiker

Als u een standaardgebruiker van uw tenant bent, vraagt u een globale beheerder om beheerders toestemming te geven voor uw toepassing. Daarvoor verstrekt u de volgende URL aan uw beheerder:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> In die URL:
> * Vervang `Enter_the_Tenant_Id_Here` door de tenant-id of tenantnaam (bijvoorbeeld `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` is de toepassings-id (client-id) voor de toepassing die u hebt geregistreerd.

Mogelijk ziet u de fout 'AADSTS50011: Er is geen antwoordadres geregistreerd voor de toepassing' nadat u de app toestemming hebt gegeven met behulp van de voorgaande URL. Deze fout t gebeurt omdat deze toepassing en de URL geen omleidings-URI hebben. U kunt dit negeren.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Stap 5: De toepassing uitvoeren

Als u een Visual Studio of Visual Studio voor Mac, drukt u op **F5** om de toepassing uit te voeren. Voer anders de toepassing uit via de opdrachtprompt, console of terminal:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
In die code:
* `{ProjectFolder}` is de map waarin u het ZIP-bestand hebt uitgepakt. Een voorbeeld is `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Als het goed is, ziet u een lijst Azure Active Directory gebruikers in de lijst.

Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als een vertrouwelijke client. Het clientgeheim wordt als tekst zonder tekst toegevoegd aan uw projectbestanden. Uit veiligheidsoverwegingen raden we u aan een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie voor meer informatie over het gebruik van een certificaat [deze instructies](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) in de GitHub-opslagplaats voor dit voorbeeld.

## <a name="more-information"></a>Meer informatie
Deze sectie bevat een overzicht van de code die vereist is voor het aanmelden van gebruikers. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, wat de belangrijkste argumenten zijn en hoe u aanmelding toevoegt aan een bestaande .NET Core-consoletoepassing.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
>
> ![Diagram waarin wordt getoond hoe de voorbeeld-app werkt die is gegenereerd in deze snelstart.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Microsoft Authentication Library (MSAL, in het [pakket Microsoft.Identity.Client)](https://www.nuget.org/packages/Microsoft.Identity.Client) is de bibliotheek die wordt gebruikt om gebruikers aan te melden en tokens aan te vragen voor toegang tot een API die wordt beveiligd door het Microsoft Identity Platform. In deze quickstart worden tokens opvragen met behulp van de eigen identiteit van de toepassing in plaats van gedelegeerde machtigingen. De verificatiestroom wordt in dit geval een [OAuth-stroom met clientreferenties genoemd.](v2-oauth2-client-creds-grant-flow.md) Zie dit artikel voor meer informatie over MSAL.NET gebruiken met een [clientreferentiestroom.](https://aka.ms/msal-net-client-credentials)

 U kunt deze MSAL.NET door de volgende opdracht uit te voeren in Visual Studio Pakketbeheer Console:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```csharp
using Microsoft.Identity.Client;
```

Initialiseer vervolgens MSAL met behulp van de volgende code:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Element | Beschrijving |
 |---------|---------|
 | `config.ClientSecret` | Het clientgeheim dat is gemaakt voor de toepassing in de Azure Portal. |
 | `config.ClientId` | De toepassings-id (client-id) voor de toepassing die is geregistreerd in Azure Portal. U vindt deze waarde op  de pagina Overzicht van de app in Azure Portal. |
 | `config.Authority`    | (Optioneel) Het STS-eindpunt (Security Token Service) dat de gebruiker kan verifiëren. Dit is meestal voor de openbare cloud, waarbij de naam `https://login.microsoftonline.com/{tenant}` van uw tenant of uw `{tenant}` tenant-id is.|

Zie de referentiedocumentatie [voor `ConfidentialClientApplication` voor meer informatie. ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>Tokens aanvragen

Als u een token wilt aanvragen met behulp van de identiteit van de app, gebruikt u de `AcquireTokenForClient` methode :

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Element| Beschrijving |
|---------|---------|
| `scopes` | Bevat de aangevraagde scopes. Voor vertrouwelijke clients moet deze waarde een indeling gebruiken die vergelijkbaar is met `{Application ID URI}/.default` . Deze indeling geeft aan dat de aangevraagde scopes de scopes zijn die statisch zijn gedefinieerd in het app-object dat is ingesteld in de Azure Portal. Voor Microsoft Graph wijst `{Application ID URI}` u naar `https://graph.microsoft.com` . Voor aangepaste web-API's `{Application ID URI}` wordt gedefinieerd in de Azure Portal, onder **Toepassingsregistratie (preview)**  >  **Een API beschikbaar maken.** |

Zie de referentiedocumentatie [voor `AcquireTokenForClient` voor meer informatie. ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk het overzicht van scenario's voor meer informatie over daemontoepassingen:

> [!div class="nextstepaction"]
> [Een daemon-app die web-API's aanroept](scenario-daemon-overview.md)
