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
ms.openlocfilehash: 547906e3d3131483468d21623744ac243090ad84
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720233"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Snelstartgids: een Token ophalen en de Microsoft Graph-API aanroepen met behulp van de identiteit van een console-app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe u met een .NET Core-consoletoepassing een toegangstoken kunt verkrijgen om de Microsoft Graph API aan te roepen en een [lijst met gebruikers](/graph/api/user-list) weer te geven in de map. In het code voorbeeld ziet u ook hoe een taak of een Windows-service kan worden uitgevoerd met een toepassings-id in plaats van de identiteit van een gebruiker. De voor beeld-console toepassing in deze Quick start is ook een daemon-toepassing, dus is het een vertrouwelijke client toepassing.

> [!div renderon="docs"]
> In het volgende diagram ziet u hoe de voor beeld-app werkt:
>
> ![Diagram waarin wordt getoond hoe de voorbeeld-app werkt die is gegenereerd in deze snelstart.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Vereisten

Deze Snelstartgids vereist [.net core 3,1](https://www.microsoft.com/net/download/dotnet-core) , maar werkt ook met .net Core 5,0.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>De app registreren en downloaden

> [!div renderon="docs" class="sxs-lookup"]
>
> U hebt twee opties om uw toepassing te bouwen: automatische of hand matige configuratie.
>
> ### <a name="automatic-configuration"></a>Automatische configuratie
>
> Als u uw app wilt registreren en automatisch wilt configureren en vervolgens het code voorbeeld wilt downloaden, voert u de volgende stappen uit:
>
> 1. Ga naar de <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">pagina Azure portal voor app-registratie</a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies voor het downloaden en automatisch configureren van uw nieuwe toepassing met één klik.
>
> ### <a name="manual-configuration"></a>Handmatige configuratie
>
> Als u uw toepassings-en code voorbeeld hand matig wilt configureren, gebruikt u de volgende procedures.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal</span></a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter voor **adres lijst en abonnementen** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de Tenant te selecteren waarin u de toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer bij **naam** een naam in voor uw toepassing. Voer bijvoorbeeld **daemon-console** in. Gebruikers van uw app krijgen deze naam te zien en u kunt deze later wijzigen.
> 1. Selecteer **Registreren** om de toepassing te maken.
> 1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
> 1. Selecteer onder **Clientgeheimen** de optie **Nieuw clientgeheim**. Voer een naam in en selecteer vervolgens **Toevoegen**. Noteer de waarde voor het geheim op een veilige locatie, voor gebruik in een latere stap.
> 1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**. Selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer in het knoop punt **gebruiker** de optie **gebruiker. lezen. alle** en selecteer vervolgens **machtigingen toevoegen**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Uw snelstart-app downloaden en configureren
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Voor het code voorbeeld in deze Snelstartgids gaat u een client geheim maken en de gebruiker van de Graph API toevoegen **. alle** machtigingen van de toepassing.
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
> Voer het project uit met behulp van Visual Studio 2019.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>Stap 3: uw Visual Studio-project configureren
>
> 1. Pak het zip-bestand uit naar een lokale map die zich dicht bij de hoofdmap van de schijf bevindt. U kunt bijvoorbeeld extra heren naar *C:\Azure-samples*.
>
>    U kunt het archief het beste uitpakken in een directory in de buurt van de hoofdmap van uw station om fouten te voor komen die worden veroorzaakt door beperkingen van de padlengte in Windows.
>
> 1. Open de oplossing in Visual Studio: *1-call-MSGraph\daemon-console.SLN* (optioneel).
> 1. Vervang in *appsettings.jsop* de waarden van `Tenant` , `ClientId` en `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    In die code:
>    - `Enter_the_Application_Id_Here` is de toepassings-ID van de toepassing die u hebt geregistreerd.
>    - Vervang door `Enter_the_Tenant_Id_Here` de Tenant-id of Tenant naam (bijvoorbeeld `contoso.microsoft.com` ).
>    - Vervang door `Enter_the_Client_Secret_Here` het client geheim dat u in stap 1 hebt gemaakt.

> [!div renderon="docs"]
> > [!TIP]
> > Ga naar de **overzichts** pagina van de app in het Azure Portal om de waarden voor de toepassings-id en de Directory-id (Tenant) te vinden. Als u een nieuwe sleutel wilt genereren, gaat u naar de pagina **certificaten & geheimen** .

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Stap 3: toestemming van de beheerder

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Stap 4: toestemming van de beheerder

Als u probeert om de toepassing op dit moment uit te voeren, ontvangt u een *HTTP-fout 403-verboden* : "onvoldoende bevoegdheden om de bewerking te volt ooien." Deze fout treedt op omdat een alleen app-machtiging een globale beheerder van uw Directory vereist om toestemming te geven aan uw toepassing. Selecteer een van de volgende opties, afhankelijk van uw rol.

##### <a name="global-tenant-administrator"></a>Globale tenantbeheerder

> [!div renderon="docs"]
> Als u een globale Tenant beheerder bent, gaat u naar **bedrijfs toepassingen** in de Azure Portal. Selecteer de registratie van uw app en selecteer **machtigingen** in het gedeelte **beveiliging** van het linkerdeel venster. Selecteer vervolgens de grote knop **toestemming verlenen beheerder voor {Tenant naam}** (waarbij **{Tenant naam}** de naam van uw directory is).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de pagina **API-machtigingen** en selecteert u **toestemming van beheerder geven voor Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Ga naar de pagina API-machtigingen]()

##### <a name="standard-user"></a>Standaardgebruiker

Als u een standaard gebruiker van uw Tenant bent, vraagt u een globale beheerder om toestemming van de beheerder voor uw toepassing te verlenen. Daarvoor verstrekt u de volgende URL aan uw beheerder:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> In die URL:
>> * Vervang door `Enter_the_Tenant_Id_Here` de Tenant-id of Tenant naam (bijvoorbeeld `contoso.microsoft.com` ).
>> * `Enter_the_Application_Id_Here` is de toepassings-ID van de toepassing die u hebt geregistreerd.

> [!NOTE]
> Mogelijk wordt de fout "AADSTS50011: er is geen antwoord adres geregistreerd voor de toepassing" weer gegeven nadat u toestemming voor de app hebt verleend met behulp van de voor gaande URL. Deze fout treedt op omdat deze toepassing en de URL geen omleidings-URI hebben. U kunt deze negeren.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Stap 5: De toepassing uitvoeren

Als u Visual Studio of Visual Studio voor Mac gebruikt, drukt u op **F5** om de toepassing uit te voeren. Als dat niet het geval is, voert u de toepassing uit via de opdracht prompt, console of terminal:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```

> In die code:
> * `{ProjectFolder}` is de map waar u het zip-bestand hebt uitgepakt. Een voorbeeld is `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Als resultaat ziet u een lijst met gebruikers in Azure Active Directory.

> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als een vertrouwelijke client. Het client geheim wordt toegevoegd als een bestand met tekst zonder opmaak aan uw project bestanden. Uit veiligheids overwegingen wordt u aangeraden een certificaat te gebruiken in plaats van een client geheim voordat u de toepassing als een productie toepassing overweegt. Zie voor meer informatie over het gebruik van een certificaat [deze instructies](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) in de GitHub-opslagplaats voor dit voorbeeld.

## <a name="more-information"></a>Meer informatie
Deze sectie bevat een overzicht van de code die vereist is voor het aanmelden van gebruikers. Dit overzicht kan handig zijn om te begrijpen hoe de code werkt, wat de belangrijkste argumenten zijn en hoe u aanmelden toevoegt aan een bestaande .NET core-console toepassing.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
>
> ![Diagram waarin wordt getoond hoe de voorbeeld-app werkt die is gegenereerd in deze snelstart.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

Micro soft Authentication Library (MSAL, in het pakket [micro soft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) ) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens om toegang te krijgen tot een API die wordt beveiligd door het micro soft Identity-platform. Deze Quick start vraagt tokens aan met behulp van de eigen identiteit van de toepassing in plaats van gedelegeerde machtigingen. De verificatie stroom in dit geval wordt een OAuth- [stroom voor client referenties](v2-oauth2-client-creds-grant-flow.md)genoemd. Zie [dit artikel](https://aka.ms/msal-net-client-credentials)voor meer informatie over het gebruik van MSAL.net met een client referentie stroom.

 U kunt MSAL.NET installeren door de volgende opdracht uit te voeren in de Visual Studio Package Manager-console:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```csharp
using Microsoft.Identity.Client;
```

Initialiseer vervolgens MSAL met de volgende code:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

> | Element | Beschrijving |
> |---------|---------|
> | `config.ClientSecret` | Het client geheim dat is gemaakt voor de toepassing in de Azure Portal. |
> | `config.ClientId` | De client-ID van de toepassing die is geregistreerd in de Azure Portal. U kunt deze waarde vinden op de **overzichts** pagina van de app in de Azure Portal. |
> | `config.Authority`    | Beschrijving Het STS-eind punt (Security Token Service) voor de gebruiker die moet worden geverifieerd. Het is doorgaans `https://login.microsoftonline.com/{tenant}` voor de open bare Cloud, waarbij `{tenant}` de naam van uw Tenant of uw Tenant-id is.|

Zie de [referentie documentatie voor voor `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)meer informatie.

### <a name="requesting-tokens"></a>Tokens aanvragen

Als u een token wilt aanvragen met behulp van de identiteit van de app, gebruikt u de `AcquireTokenForClient` volgende methode:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Element| Beschrijving |
> |---------|---------|
> | `scopes` | Bevat de aangevraagde bereiken. Voor vertrouwelijke clients moet deze waarde een indeling hebben die vergelijkbaar is met `{Application ID URI}/.default` . Deze indeling geeft aan dat de aangevraagde scopes zijn die statisch zijn gedefinieerd in het app-object dat in de Azure Portal is ingesteld. Voor Microsoft Graph `{Application ID URI}` verwijst naar `https://graph.microsoft.com` . Voor aangepaste web-api's `{Application ID URI}` wordt gedefinieerd in de Azure portal onder **toepassings registratie (preview)**  >  **een API** weer te geven. |

Zie de [referentie documentatie voor voor `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)meer informatie.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk het overzicht van scenario's voor meer informatie over daemontoepassingen:

> [!div class="nextstepaction"]
> [Een daemon-app die web-API's aanroept](scenario-daemon-overview.md)
