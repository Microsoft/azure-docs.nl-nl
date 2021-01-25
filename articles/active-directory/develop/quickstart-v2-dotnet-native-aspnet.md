---
title: 'Quick Start: een ASP.NET-Web-API aanroepen die wordt beveiligd door het micro soft-identiteits platform | Azure'
titleSuffix: Microsoft identity platform
description: In deze Quick Start leert u hoe u een ASP.NET-Web-API aanroept die wordt beveiligd door het micro soft-identiteits platform van een Windows Desktop-toepassing (WPF).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: ec8fd05c0661178cc07b9165793c9f34f2463948
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98754316"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Quickstart: Een ASP.NET-web-API aanroepen die wordt beveiligd door Microsoft-identiteitsplatform

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe u een ASP.NET-web-API kunt beveiligen door de toegang tot bijbehorende resources te beperken tot alleen geautoriseerde accounts. Het voorbeeld biedt ondersteuning voor de autorisatie van persoonlijke Microsoft-accounts en accounts in elke willekeurige Azure AD-organisatie (Azure Active Directory).

In dit artikel wordt ook een WPF-app (Windows Presentation Foundation) gebruikt om te laten zien hoe u een toegangstoken kunt aanvragen om toegang te krijgen tot een web-API.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Visual Studio 2017 of 2019. U kunt [Visual Studio gratis](https://www.visualstudio.com/downloads/) downloaden.

## <a name="clone-or-download-the-sample"></a>Het voorbeeld klonen of downloaden

U kunt het voorbeeld op twee manieren ophalen:

* Kloon het vanaf uw shell of opdrachtregel:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Download het als een ZIP-bestand](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

## <a name="register-your-web-api"></a>Uw web-API registreren

In deze sectie registreert u uw web-API in **App-registraties** in Azure Portal.

### <a name="choose-your-azure-ad-tenant"></a>Uw Azure Active Directory-tenant kiezen

Als u uw apps handmatig wilt registreren, kiest u de Azure AD-tenant (Active Directory) waar u uw apps wilt maken.

1. Meld u bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a> aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Als uw account zich in meer dan één Azure AD-tenant bevindt, selecteert u uw profiel rechtsboven en selecteert u **Schakelen tussen mappen**.
1. Stel de portalsessie in op de gewenste Azure AD-tenant.

### <a name="register-the-todolistservice-app"></a>De TodoListService-app registreren

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
1. Bij **Ondersteunde accounttypen** selecteert u **Accounts in een organisatieadreslijst**.
1. Selecteer **Registreren** om de toepassing te maken.
1. Zoek de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** en noteer deze voor gebruik later. U hebt deze nodig om het Visual Studio-configuratiebestand van dit project te configureren (dat is `ClientId` in het bestand *TodoListService\Web.config*).
1. Selecteer onder **Beheren** **Een API beschikbaar maken** > **Een bereik toevoegen**. Accepteer de voorgestelde URI voor de toepassings-id (`api://{clientId}`) door **Opslaan en doorgaan** te selecteren en de volgende informatie op te geven:

    1. Voer `access_as_user` in bij **Bereiknaam**.
    1. Zorg ervoor dat de optie **Beheerders en gebruikers** is geselecteerd voor **Wie kan toestemming verlenen?**
    1. Voer in het vak **Weergavenaam van de beheerderstoestemming** als naam `Access TodoListService as a user` in.
    1. Voer in het vak **Beschrijving van de beheerderstoestemming** de tekst `Accesses the TodoListService web API as a user` in.
    1. Voer in het vak **Weergavenaam van de gebruikerstoestemming** als naam `Access TodoListService as a user` in.
    1. Voer in het vak Beschrijving van **toestemming van de gebruiker** `Accesses the TodoListService web API as a user`.
    1. Laat **Status** op **Ingeschakeld** staan.
1. Selecteer **Bereik toevoegen**.

### <a name="configure-the-service-project"></a>Het serviceproject configureren

Configureer als volgt het serviceproject zodat dit overeenkomt met de geregistreerde web-API:

1. Open de oplossing in Visual Studio en open vervolgens het bestand *Web.config* in de hoofdmap van het TodoListService-project.

1. Vervang de waarde van de parameter `ida:ClientId` door de waarde voor Client-id (Toepassings-id) uit de toepassing die u zojuist hebt geregistreerd in de portal voor **toepassingsregistratie**.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Het nieuwe bereik toevoegen aan het bestand app.config

U kunt het nieuwe bereik als volgt toevoegen aan het TodoListClient-bestand *app.config*:

1. Open het bestand *app.config* in de hoofdmap van het TodoListClient-project.

1. Plak de toepassings-id uit de toepassing die u zojuist hebt geregistreerd voor uw TodoListService-project in de parameter `TodoListServiceScope` en vervang de tekenreeks `{Enter the Application ID of your TodoListService from the app registration portal}`.

  > [!NOTE]
  > Zorg dat de toepassings-id de volgende notatie heeft: `api://{TodoListService-Application-ID}/access_as_user` (waarbij `{TodoListService-Application-ID}` de GUID is die de toepassings-id voor uw TodoListService-app vertegenwoordigt).

## <a name="register-the-todolistclient-client-app"></a>De TodoListClient-app registreren

In deze sectie registreert u de TodoListClient-app in **App-registraties** in de Azure-portal en configureert u vervolgens de code in het TodoListClient-project. Als de client en server worden beschouwd als *dezelfde toepassing*, kunt u dezelfde toepassing die is geregistreerd in stap 2 opnieuw gebruiken. Gebruik dezelfde toepassing als u wilt dat gebruikers zich aanmelden met een persoonlijk Microsoft-account.

### <a name="register-the-app"></a>De app registreren

Ga als volgt te werk om de TodoListClient-app te registreren:

1. Ga naar [App-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) op het Microsoft Identity Platform voor ontwikkelaars.
1. Selecteer **Nieuwe registratie**.
1. Wanneer de pagina **Een toepassing registreren** wordt geopend, voert u de registratiegegevens van de toepassing in:

    1. Voer in de sectie **Naam** een beschrijvende toepassingsnaam die gebruikers van de app te zien krijgen. Bijvoorbeeld: **NativeClient-DotNet-TodoListClient**.
    1. Bij **Ondersteunde accounttypen** selecteert u **Accounts in een organisatieadreslijst**.
    1. Selecteer **Registreren** om de toepassing te maken.

   > [!NOTE]
   > In het bestand *app.config* van het TodoListClient-project is de standaardwaarde voor `ida:Tenant` ingesteld op `common`. De mogelijke waarden zijn:
   > - `common`: u kunt zich aanmelden met behulp van een werk- of schoolaccount of een persoonlijk Microsoft-account (omdat u **Accounts in elke organisatiedirectory** hebt geselecteerd in stap 3b).
   > - `organizations`: u kunt zich aanmelden met behulp van een werk- of schoolaccount.
   > - `consumers`: u kunt zich alleen aanmelden met behulp van een persoonlijk Microsoft-account.

1. Selecteer op de pagina **Overzicht** van de app de optie **Verificatie** en doe dan het volgende:

    1. Selecteer onder **Platformconfiguraties** de knop **Een platform toevoegen**.
    1. Bij **Mobiele toepassingen en bureaubladtoepassingen** selecteert u **Mobiele toepassingen en bureaubladtoepassingen**.
    1. Schakel bij **Omleidings-URI's** het selectievakje **https://login.microsoftonline.com/common/oauth2/nativeclient** in.
    1. Selecteer **Configureren**.

1. Selecteer **API-machtigingen** en ga dan als volgt te werk:

    1. Selecteer de knop **Een machtiging toevoegen**.
    1. Selecteer het tabblad **Mijn API's**.
    1. Selecteer in de lijst met API's **AppModelv2-NativeClient-DotNet-TodoListService-API** of de naam die u hebt ingevoerd voor de web-API.
    1. Schakel de machtiging **access_as_user** in als dit selectievakje nog niet is ingeschakeld. Gebruik zo nodig het zoekvak.
    1. Selecteer de knop **Toestemmingen toevoegen**.

### <a name="configure-your-project"></a>Het project configureren

Ga als volgt te werk om uw TodoListClient-project te configureren:

1. In de portal **App-registraties** kopieert u vanaf de pagina **Overzicht** de waarde van de **Toepassings-id (client)** .

1. Open het bestand *app.config* vanuit de hoofdmap van het TodoListClient-project en plak de waarde van de Toepassings-id in de `ida:ClientId`-parameter.

## <a name="run-your-todolistclient-project"></a>Uw TodoListClient-project uitvoeren

Ga als volgt te werk om uw TodoListClient-project uit te voeren:

1. Druk op F5 om uw TodoListClient-project te openen. De projectpagina wordt geopend.

1. Selecteer **Aanmelden** rechtsboven en meld u aan met dezelfde aanmeldingsgegevens die u hebt gebruikt om uw toepassing te registreren of meld u aan als een gebruiker in dezelfde map.

   Als u zich voor de eerste keer aanmeldt, wordt u mogelijk gevraagd om toestemming te geven voor de TodoListService-web-API.

   De aanmelding vraagt ook het toegangstoken voor het bereik *access_as_user* om u toegang te geven tot de TodoListService-web-API en om de *takenlijst* te bewerken.

## <a name="pre-authorize-your-client-application"></a>Uw clienttoepassing vooraf autoriseren

Een manier om gebruikers uit andere mappen toegang te geven tot uw web-API is de clienttoepassing vooraf te autoriseren voor toegang tot uw web-API. U doet dat door de toepassings-id van de client-app toe te voegen aan de lijst met vooraf geautoriseerde toepassingen voor uw web-API. Door een vooraf geautoriseerde client toe te voegen, zorgt u ervoor dat gebruikers toegang hebben tot uw web-API zonder dat u toestemming hoeft te geven. Als u uw client-app vooraf wilt autoriseren, gaat u als volgt te werk:

1. Open de eigenschappen van uw TodoListService-app in de portal **App-registraties**.
1. In de sectie **Een API beschikbaar maken**, onder **Geautoriseerde clienttoepassingen**, selecteert u **Een clienttoepassing toevoegen**.
1. Plak in het vak **Client-id** de toepassings-id van de TodoListClient-app.
1. Selecteer in de sectie **Gemachtigde bereiken** het bereik voor de web-API `api://<Application ID>/access_as_user`.
1. Selecteer **Toepassing toevoegen**.

### <a name="run-your-project"></a>Uw project uitvoeren

1. Druk op F5 om het project uit te voeren. Als het goed is, wordt uw TodoListClient-app geopend.
1. Selecteer **Aanmelden** rechtsboven en meld u vervolgens aan met een persoonlijk Microsoft-account, zoals live.com of hotmail.com, of met een werk- of schoolaccount.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Optioneel: Aanmeldingstoegang beperken tot bepaalde gebruikers

Wanneer u de voorgaande stappen hebt gevolgd, kunnen persoonlijke accounts, zoals outlook.com en live.com, of werk- of schoolaccounts van organisaties die geïntegreerd zijn met Azure AD, tokens aanvragen en toegang krijgen tot uw web-API. Dit is de standaardinstelling.

Als u wilt bepalen wie zich kan aanmelden bij uw toepassing, gebruikt u een van de volgende opties:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>Optie 1: Toegang beperken tot één organisatie (één tenant)

U kunt aanmeldingstoegang voor uw toepassing beperken tot gebruikersaccounts die zich in één Azure AD-tenant bevinden, waaronder *gastaccounts* van die tenant. Dit scenario is gebruikelijk voor *Line-Of-Business-toepassingen*.

1. Open het bestand *App_Start \Startup.Auth* en wijzig de waarde van het eindpunt voor metagegevens dat wordt doorgegeven aan de `OpenIdConnectSecurityTokenProvider` in `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"`. U kunt ook de naam van de tenant gebruiken, bijvoorbeeld `contoso.onmicrosoft.com`.
2. Stel in hetzelfde bestand de eigenschap `ValidIssuer` op de `TokenValidationParameters` in op `"https://sts.windows.net/{Tenant ID}/"` en stel het argument `ValidateIssuer` in op `true`.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>Optie 2: Een aangepaste methode gebruiken om uitgevers te valideren

U kunt een aangepaste methode implementeren om uitgevers te valideren met behulp van de parameter `IssuerValidator`. Als u meer informatie wilt over deze parameter, bekijk dan de [TokenValidationParameters](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters?view=azure-dotnet&preserve-view=true)-klasse.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het beveiligde web-appscenario dat het Microsoft Identity-platform ondersteunt:
> [!div class="nextstepaction"]
> [Scenario met beveiligde web-API](scenario-protected-web-api-overview.md)
