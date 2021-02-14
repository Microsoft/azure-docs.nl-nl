---
title: 'Zelfstudie: Een multitenant-daemon bouwen die toegang heeft tot Microsoft Graph-bedrijfsgegevens | Azure'
titleSuffix: Microsoft identity platform
description: In deze zelfstudie leert u hoe u een ASP.NET-web-API die wordt beveiligd door Azure Active Directory aanroept vanuit een Windows-desktoptoepassing (WPF). De WPF-client verifieert een gebruiker, vraagt een toegangstoken aan en roept de web-API aan.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 4fccff70fd267aef84550b4e2f5d6f5f9422a341
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103019"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>Zelfstudie: Een multitenant-daemon bouwen die het Microsoft-identiteitsplatform gebruikt

In deze zelfstudie downloadt en voert u een ASP.NET-daemon-web-app uit die laat zien hoe u de OAuth 2,0-client referenties verleent om een toegangstoken op te halen om de Microsoft Graph API aan te roepen.

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Een daemon-app integreren bij het Microsoft-identiteitsplatform
> * Toepassingstoestemming rechtstreeks aan de app toekennen door een beheerder
> * Een toegangstoken ophalen om de Microsoft Graph-API aan te roepen
> * De Microsoft Graph API aanroepen.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- [Visual Studio 2017 of 2019](https://visualstudio.microsoft.com/downloads/).
- Een Azure AD-tenant. Zie [Een Azure AD-tenant verkrijgen](quickstart-create-new-tenant.md) voor meer informatie.
- Eén of meer gebruikersaccounts in uw Azure AD-tenant. Dit voorbeeld werkt niet met een Microsoft-account. Als u bent aangemeld bij de [Azure-portal](https://portal.azure.com) met een Microsoft-account en nog nooit een gebruikersaccount hebt gemaakt in uw map, moet u dat nu doen.

## <a name="scenario"></a>Scenario

De app is gebouwd als een ASP.NET MVC-toepassing. De OWIN OpenID Connect-middleware wordt gebruikt om gebruikers aan te melden.

Het daemon-component in dit voorbeeld is een API-controller, `SyncController.cs`. Wanneer de controller wordt aangeroepen, wordt er een lijst met gebruikers in de Azure AD-tenant (Azure Active Directory) van de gebruiker opgehaald uit Microsoft Graph. `SyncController.cs` wordt geactiveerd door een AJAX-aanroep in de webtoepassing. [Microsoft Authentication Library (MSAL) voor .NET](msal-overview.md) wordt gebruikt om een toegangstoken op te halen voor Microsoft Graph.

Omdat de app een multitenant-app voor zakelijke Microsoft-klanten is, moet deze een manier bieden voor klanten om de toepassing te 'registreren' of 'te verbinden' met hun bedrijfsgegevens. Tijdens de verbindings stroom verleent een globale beheerder eerst *toepassings machtigingen* rechtstreeks aan de app, zodat deze toegang heeft tot Bedrijfs gegevens op een niet-interactieve manier zonder de aanwezigheid van een aangemelde gebruiker. Het merendeel van de logica in dit voorbeeld toont hoe deze verbindingsstroom kan worden gerealiseerd met het [beheerderstoestemming](v2-permissions-and-consent.md#using-the-admin-consent-endpoint)-eindpunt van het identiteitsplatform.

![Diagram waarin de UserSync-app wordt weergegeven met drie lokale items die verbinding maken met Azure. Hierbij wordt voor Startup.Auth een token interactief opgehaald om verbinding te maken met Azure AD, wordt voor AccountController toestemming van de beheerder verkregen om verbinding te maken met Azure AD en worden met SyncController gebruikers gelezen om verbinding te maken met Microsoft Graph.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Meer informatie over de concepten die in dit voor beeld worden gebruikt, vindt u in de documentatie over het [client referentie protocol voor het identiteits platform](v2-oauth2-client-creds-grant-flow.md).

## <a name="clone-or-download-this-repository"></a>Deze opslagplaats klonen of downloaden

Voer vanaf uw shell of opdrachtregel deze opdracht in:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Of [download het voorbeeld in een zip-bestand](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Uw toepassing registreren

Dit voorbeeld heeft één project. Voor het registreren van de toepassing bij uw Azure AD-tenant kunt u:

- De stappen volgen in [Het voorbeeld registreren met uw Azure Active Directory-tenant](#register-the-client-app-dotnet-web-daemon-v2) en [Het voorbeeld configureren om uw Azure AD-tenant te gebruiken](#choose-the-azure-ad-tenant).
- PowerShell-voorbeeldscripts gebruiken die:
  - *automatisch* de Azure AD-toepassingen en bijbehorende objecten (wachtwoorden, machtigingen, afhankelijkheden) voor u maken.
  - De configuratiebestanden wijzigen van het Visual Studio-project.

Als u de automatisering wilt gebruiken:

1. Voer PowerShell uit in Windows en ga naar de hoofdmap van de gekloonde map.
1. Voer deze opdracht uit:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Voer het script uit om uw Azure AD-toepassing te maken en configureer de code van de voorbeeldtoepassing dienovereenkomstig:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Andere manieren om scripts uit te voeren, worden beschreven in [Script voor het maken van apps](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md).

1. Open de Visual Studio-oplossing en selecteer **Start** om de code uit te voeren.

Als u de automatisering niet wilt gebruiken, gebruikt u de stappen in de volgende secties.

### <a name="choose-the-azure-ad-tenant"></a>De Azure AD-tenant kiezen

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>De client-app registreren (dotnet-web-daemon-v2)

1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst**.
1. Selecteer in de sectie **URI omleiden (optioneel)** de optie **Web** in de keuzelijst, en voer `https://localhost:44316/` en `https://localhost:44316/Account/GrantPermissions` in als Omleidings-URI's.

    Als er meer dan twee omleidings-URI's zijn, moet u ze later, nadat de app is gemaakt, toevoegen vanuit het tabblad **Verificatie**.
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** voor later gebruik. U hebt deze nodig om het Visual Studio-configuratiebestand van dit project te configureren.
1. Selecteer **Verificatie** onder **Beheren**.
1. Stel de **afmeldings-URL** voor het kanaal in op `https://localhost:44316/Account/EndSession` .
1. Selecteer in de sectie **impliciete toekenning en hybride stromen** **toegangs tokens** en **id-tokens**. Voor dit voorbeeld moet de [impliciete toekenningsstroom](v2-oauth2-implicit-grant-flow.md) zijn ingeschakeld om de gebruiker aan te melden en een API aan te roepen.
1. Selecteer **Opslaan**.
1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
1. Selecteer in de sectie **Clientgeheimen** de optie **Nieuw clientgeheim**. 
1. Voer een beschrijving in (bijvoorbeeld: **app-geheim**).
1. Selecteer wanneer de sleutel vervalt: **Over één jaar**, **Over twee jaar** of **Vervalt nooit**.
1. Selecteer **Toevoegen**. Noteer de sleutelwaarde op een veilige locatie. U hebt deze sleutel later nodig om het project in Visual Studio te configureren.
1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**.
1. Selecteer in de sectie **Veelgebruikte Microsoft-API's** de optie **Microsoft Graph**.
1. Zorg ervoor dat in de sectie **Toepassingstoestemmingen** de juiste machtigingen zijn geselecteerd: **User.Read.All**.
1. Selecteer **Machtigingen toevoegen**.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Het voorbeeld configureren om uw Azure AD-tenant te gebruiken

In de volgende stappen is **ClientID** hetzelfde als 'toepassings-ID' of **AppId**.

Open de oplossing in Visual Studio om de projecten te configureren.

### <a name="configure-the-client-project"></a>Het client-project configureren

Als u de scripts hebt gebruikt, zijn de volgende wijzigingen voor u aangebracht.

1. Open het bestand **UserSync\Web.Config**.
1. Zoek de app-sleutel **ida:ClientId**. Vervang de bestaande waarde met de toepassings-ID van de toepassing **dotnet-web-daemon-v2** die is gekopieerd van de Azure-portal.
1. Zoek de app-sleutel **ida:ClientSecret**. Vervang de bestaande waarde met de sleutel die u hebt opgeslagen tijdens het maken van de **dotnet-web-daemon-v2**-app in de Azure-portal.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Schoon de oplossing op, bouw de oplossing opnieuw, voer de toepassing UserSync uit en meld u vervolgens aan als beheerder in uw Azure AD-tenant. Als u geen Azure AD-tenant hebt om te testen, kunt u [deze instructies volgen](quickstart-create-new-tenant.md) om er een te krijgen.

Wanneer u zich aanmeldt, wordt u eerst om toestemming gevraagd om u aan te laten melden en uw gebruikersprofiel te laten lezen door de app. Deze toestemming maakt het mogelijk voor de app om te controleren dat u een zakelijke gebruiker bent.

![Gebruikerstoestemming](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

De app probeert vervolgens een lijst met gebruikers van uw Azure AD-tenant te synchroniseren via Microsoft Graph. Als dat niet mogelijk is, wordt u (de tenantbeheerder) gevraagd uw tenant te verbinden met de app.

Vervolgens wordt door de app toestemming gevraagd om de lijst met gebruikers in uw tenant te lezen.

![toestemming van de beheerder](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Nadat u toestemming hebt gegeven, wordt u afgemeld bij de app. Deze afmelding zorgt ervoor dat bestaande toegangstokens voor Microsoft Graph worden verwijderd uit de tokencache. Wanneer u zich opnieuw aanmeldt, heeft het nieuwe token dat is opgehaald de benodigde toestemming om aanroepen richting Microsoft Graph te maken.


Wanneer u de toestemming hebt gegeven, kan de app op elk moment zoeken naar gebruikers. U kunt dit verifiëren door de knop **Gebruikers synchroniseren** te selecteren en de lijst met gebruikers te vernieuwen. Probeer een gebruiker toe te voegen of te verwijderen en de lijst opnieuw te synchroniseren. (Maar let op, de app synchroniseert alleen de eerste pagina met gebruikers.)

## <a name="about-the-code"></a>Over de code

De relevante code voor dit voorbeeld staat in de volgende bestanden:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Initiële aanmelding. Met name de acties op de controller hebben een **Autorisatie**-attribuut, waardoor de gebruiker is gedwongen zich aan te melden. De toepassing gebruikt de [autorisatiecodestroom](v2-oauth2-auth-code-flow.md) om de gebruiker aan te melden.
- **Controllers\SyncController.cs**: De lijst met gebruikers met de lokale opslag in het geheugen synchroniseren.
- **Controllers\UserController.cs**: De lijst met gebruikers van de lokale opslag in het geheugen weergeven.
- **Controllers\AccountController.cs**: Toestemming krijgen van de tenantbeheerder door gebruik te maken van het beheerderstoestemmingseindpunt.

## <a name="re-create-the-sample-app"></a>De voorbeeld-app opnieuw bouwen

1. Maak in Visual Studio een nieuw **Visual C#** -project voor een **ASP.NET-webtoepassing (.NET Framework)**
1. Kies in het volgende scherm de **MVC**-projectsjabloon. Voeg ook map- en core-referenties toe voor **Web-API**, want later voegt u een Web-API-controller toe. Houd de verificatiemodus van het project standaard: **Geen verificatie**.
1. Selecteer het project in het venster **Solution Explorer** en selecteer de toets **F4**.
1. Stel in de projecteigenschappen **SSL ingeschakeld** in op **Waar**. Noteer de gegevens in **SSL URL**. Die hebt u nodig wanneer u de registratie van deze toepassing in de Azure-portal configureert.
1. Voeg de volgende ASP.NET OWIN-middleware-NuGet-pakketten toe:
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. In de map **App_Start**:
   1. Maak een klasse met de naam **Startup.Auth.cs**.
   1. Verwijder **.App_Start** uit de naam van naamruimte.
   1. Vervang de code voor de klasse **Opstarten** met de code van hetzelfde bestand van de voorbeeldapp.
   Zorg ervoor dat u de volledige klassedefinitie vervangt. De definitie wijzigt van **openbare klasse Opstarten** naar **openbare gedeeltelijke klasse Opstarten.**
1. Los ontbrekende verwijzingen in **Startup.Auth.cs** op door **Using**-instructies toe te voegen zoals voorgesteld door Visual Studio IntelliSense.
1. Klik met de rechtermuisknop op het project, selecteer **Toevoegen** en vervolgens **Klasse**.
1. Typ **OWIN** in het zoekvak. De **OWIN-opstartklasse** wordt weergegeven als selectie. Selecteer deze klasse en geef deze de naam **Startup.cs**.
1. In **Startup.cs** vervangt u de code voor de klasse **Opstarten** met de code van hetzelfde bestand van de voorbeeldapp. De definitie wijzigt wederom van **openbare klasse Opstarten** naar **openbare gedeeltelijke klasse Opstarten.**
1. Voeg in de map **Modellen** een nieuwe klasse toe met de naam **MsGraphUser.cs**. Vervang de implementatie met de inhoud van het bestand met dezelfde naam van het voorbeeld.
1. Voeg een nieuwe instantie **MVC 5 Controller - Empty** toe met de naam **AccountController**. Vervang de implementatie met de inhoud van het bestand met dezelfde naam van het voorbeeld.
1. Voeg een nieuwe instantie **MVC 5 Controller - Empty** toe met de naam **UserController**. Vervang de implementatie met de inhoud van het bestand met dezelfde naam van het voorbeeld.
1. Voeg een nieuwe instantie **Web API 2 Controller - Empty** toe met de naam **SyncController**. Vervang de implementatie met de inhoud van het bestand met dezelfde naam van het voorbeeld.
1. Voeg voor de gebruikersinterface in de map **Views\Account** drie instanties **Empty (without model) Views** toe met de namen **GrantPermissions**, **Index** en **UserMismatch**. Voeg een andere met de naam **Index** toe in de map **Views\User**. Vervang de implementatie met de inhoud van het bestand met dezelfde naam van het voorbeeld.
1. Werk **Shared\_Layout.cshtml** en **Home\Index.cshtml** bij om de verschillende weergaven correct aan elkaar te koppelen.

## <a name="deploy-the-sample-to-azure"></a>Het voorbeeld implementeren in Azure

Dit project heeft web-app- en web-API-projecten. Om deze op Azure-websites te implementeren, neemt u voor elk project de volgende stappen:

1. Maak een Azure-website.
1. Publiceer de web-app en web-API's op de website.
1. Werk clients bij zodat ze de website aanroepen in plaats van IIS Express.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Maak en publiceer dotnet-web-daemon-v2 op een Azure-website

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Selecteer in de linkerbovenhoek  **Een resource maken**.
1. Selecteer **Web** > **Web-apps** en geef uw website een naam. Noem de site bijvoorbeeld **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Selecteer de gegevens voor **Abonnement**, **Resourcegroep** en **App-serviceabonnement en -lokatie**. **OS** is **Windows** en **Publiceren** is **Code**.
1. Selecteer **Maken** en wacht totdat de app-service is gemaakt.
1. Wanneer u de melding **Implementatie geslaagd** krijgt, selecteert u **Naar resource gaan** om naar de zojuist gemaakte app-service te gaan.
1. Nadat de website is gemaakt, kunt u die zoeken in het **Dashboard** en selecteren om het scherm **Overzicht** van de app-service te openen.
1. Download in het tabblad **Overzicht** van de app-service het publicatieprofiel door de koppeling **Publicatieprofiel ophalen** te selecteren en op te slaan. U kunt andere implementatiemechanismen gebruiken, zoals implementeren vanuit broncodebeheer.
1. Schakel naar Visual Studio en:
   1. Ga naar het project **dotnet-web-daemon-v2**.
   1. Klik met de rechtermuisknop op het project in Solution Explorer en klik vervolgens op **Publiceren**.
   1. Selecteer in de onderste balk **Profiel importeren** en importeer het publicatieprofiel dat u eerder hebt gedownload.
1. Selecteer **Configureren**.
1. Werk in het tabblad **Verbinding** de doel-URL bij, zodat deze 'https' gebruikt. Gebruik bijvoorbeeld `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`. Selecteer **Next**.
1. Zorg er in het tabblad **Instellingen** voor dat **Organisatieverificatie inschakelen** leeg is.
1. Selecteer **Opslaan**. Selecteer in het hoofdscherm **Publiceren**.

Visual Studio publiceert het project en opent automatisch een browser naar de URL van het project. Als u de standaardwebpagina van het project ziet is de publicatie geslaagd.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>De toepassingsregistratie van de Azure AD-tenant bijwerken voor dotnet-web-daemon-v2

1. Ga terug naar <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Selecteer in het linkervenster de **Azure Active Directory**-service en selecteer vervolgens **App-registraties**.
1. Selecteer de toepassing **dotnet-web-daemon-v2**.
1. Werk op de pagina **verificatie** voor uw toepassing de velden **voor de afmeldings-URL voor front-Channel** bij met het adres van uw service. Gebruik bijvoorbeeld `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession`.
1. Werk in het menu **Huisstijl** de **URL van de startpagina** bij met het adres van uw service. Gebruik bijvoorbeeld `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`.
1. Sla de configuratie op.
1. Voeg dezelfde URL toe aan de lijst met waarden van het menu **Verificatie** > **Omleidings-URL**. Als u meerdere omleidings-URL's hebt, zorg er dan voor dat er een nieuwe vermelding is die de URI van de app-service gebruikt voor elke omleidings-URL.

## <a name="clean-up-resources"></a>Resources opschonen
Verwijder het app-object dat u hebt gemaakt in de stap [Uw toepassing registreren](#register-your-application) als u het niet meer nodig hebt.  Om de toepassing te verwijderen volgt u de instructies in [Een toepassing verwijderen die is geschreven door u of uw organisatie](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Help opvragen

Gebruik [micro soft Q&A](https://docs.microsoft.com/answers/products/) om ondersteuning van de community te krijgen.
Stel uw vragen op [micro soft Q&A](https://docs.microsoft.com/answers/products/) als eerste en blader door bestaande problemen om te zien of iemand uw vraag eerder heeft gesteld.
Zorg ervoor dat uw vragen of opmerkingen zijn gelabeld met ' Azure-AD-adal-afschaffing ', ' Azure-AD-msal ' en ' DotNet-Standard '.

Als u een fout vindt in het voorbeeld, meld het probleem dan in [GitHub-problemen](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Als u een fout vindt in MSAL.NET, meld het probleem dan in [MSAL.NET GitHub-problemen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Ga naar de [pagina Gebruikersspraak](https://feedback.azure.com/forums/169401-azure-active-directory) om een aanbeveling te doen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het bouwen van daemon-apps die gebruikmaken van het Microsoft-identiteitsplatform voor toegang tot beveiligde web-API's:

> [!div class="nextstepaction"]
> [Scenario: Een daemon-app die web-API's aanroept](scenario-daemon-overview.md)
