---
title: 'Quickstart: Microsoft Graph aanroepen vanuit een Python-daemon | Azure'
titleSuffix: Microsoft identity platform
description: In deze Quick Start leert u hoe een python-proces een toegangs token kan ophalen en een API aanroept die wordt beveiligd door micro soft Identity platform, met behulp van de eigen identiteit van de app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/22/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 734fad7d3f4fb7a2a816d9ad10fb6b15e2faf9e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99820399"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-python-console-app-using-apps-identity"></a>Quickstart: Een token verkrijgen en Microsoft Graph API aanroepen vanuit een Python-console-app met behulp van de identiteit van de app

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe u met een Python-toepassing een toegangstoken kunt krijgen, met behulp van de app-id, om de Microsoft Graph API aan te roepen en een [lijst met gebruikers](/graph/api/user-list) weer te geven in de map. Het codevoorbeeld laat zien hoe een taak of Windows-service zonder toezicht kan worden uitgevoerd met een toepassings-id, in plaats van een gebruikers-id. 

> [!div renderon="docs"]
> ![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze snelstart](media/quickstart-v2-python-daemon/python-console-daemon.svg)

## <a name="prerequisites"></a>Vereisten

Als u dit voorbeeld wilt uitvoeren, hebt u het volgende nodig:

- [Python 2.7+](https://www.python.org/downloads/release/python-2713) of [Python 3+](https://www.python.org/downloads/release/python-364/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden

> [!div renderon="docs" class="sxs-lookup"]
>
> U hebt twee opties voor het starten van de snelstarttoepassing: Express (optie 1 hieronder) en handmatig (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de quickstart-ervaring <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonDaemonQuickstartPage/sourceType/docs" target="_blank">Azure-portal - App-registraties</a>.
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Zoek en selecteer de optie **Azure Active Directory**.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer **Registreren**.
> 1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**.
> 1. Selecteer onder **Clientgeheimen** de optie **Nieuw clientgeheim**. Voer een naam in en selecteer vervolgens **Toevoegen**. Noteer de waarde voor het geheim op een veilige locatie, voor gebruik in een latere stap.
> 1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**. Selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer onder het knooppunt **Gebruiker** de optie **User.Read.All**. Selecteer vervolgens **Machtigingen toevoegen**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-quickstart-app"></a>De Quick Start-app downloaden en configureren
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
> Voor het code voorbeeld in deze Snelstartgids werkt u een client geheim en voegt u de gebruiker van de Graph API **. alle** machtigingen voor alle toepassingen toe.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-netcore-daemon/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-the-python-project"></a>Stap 2: het python-project downloaden

> [!div renderon="docs"]
> [Het Python-deamonproject downloaden](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-daemon/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


> [!div renderon="docs"]
> #### <a name="step-3-configure-the-python-project"></a>Stap 3: het python-project configureren
>
> 1. Pak het zip-bestand uit in een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld **C:\Azure-Samples**.
> 1. Ga naar de submap **1-call-MSGraph-WithSecret**.
> 1. Bewerk **parameters.json** en vervang de waarden van de velden `authority`, `client_id` en `secret` door het volgende fragment:
>
>    ```json
>    "authority": "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
>    "client_id": "Enter_the_Application_Id_Here",
>    "secret": "Enter_the_Client_Secret_Here"
>    ```
>    Waar:
>    - `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
>    - `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>    - `Enter_the_Client_Secret_Here`: vervang deze waarde door het clientgeheim dat is gemaakt in stap 1.
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** en **Map-id (tenant-id)** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal. Voor het genereren van een nieuwe sleutel gaat u naar de pagina **Certificaten en geheimen**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Stap 3: toestemming van de beheerder

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Stap 4: toestemming van de beheerder

Als u op dit moment probeert de toepassing uit te voeren, krijgt u de foutmelding *HTTP 403: verboden*: `Insufficient privileges to complete the operation`. Deze fout treedt op, omdat voor elke *alleen-app-toestemming* beheerderstoestemming nodig is, wat betekent dat een globale beheerder van uw map toestemming moet geven aan uw toepassing. Selecteer een van de opties hieronder, afhankelijk van uw rol:

##### <a name="global-tenant-administrator"></a>Globale tenantbeheerder

> [!div renderon="docs"]
> Als u een globale Tenant beheerder bent, gaat u naar de pagina met **API-machtigingen** in **App-registraties** in de Azure Portal en selecteert u **toestemming van de beheerder toekennen voor {Tenant naam}** (waarbij {Tenant naam} de naam van uw directory is).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de pagina **API-machtigingen** en selecteert u **beheerder toestemming geven voor Enter_the_Tenant_Name_Here**.
> > [!div id="apipermissionspage"]
> > [Ga naar de pagina API-machtigingen]()

##### <a name="standard-user"></a>Standaardgebruiker

Als u een standaard gebruiker van uw Tenant bent, vraagt u een globale beheerder om toestemming van de beheerder voor uw toepassing te verlenen. Daarvoor verstrekt u de volgende URL aan uw beheerder:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Waar:
>> * `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **Tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Stap 4: De toepassing uitvoeren

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Stap 5: De toepassing uitvoeren

U moet de afhankelijkheden van dit voor beeld één keer installeren.

```console
pip install -r requirements.txt
```

Voer dan de toepassing uit via de opdrachtprompt of console:

```console
python confidential_client_secret_sample.py parameters.json
```

U moet op de console-uitvoer een JSON-fragment zien met een lijst van gebruikers in uw Azure AD-map.

> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie voor meer informatie over het gebruik van een certificaat [deze instructies](https://github.com/Azure-Samples/ms-identity-python-daemon/blob/master/2-Call-MsGraph-WithCertificate/README.md) in dezelfde github-opslag plaats voor dit voor beeld, maar in de tweede map **2-Call-MSGraph-WithCertificate**.

## <a name="more-information"></a>Meer informatie

### <a name="msal-python"></a>MSAL Python

[MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en de aanvraagtokens die worden gebruikt voor toegang tot een API die is beveiligd via het Microsoft-identiteitsplatform. Zoals beschreven, worden met deze snelstart tokens aangevraagd met behulp van de eigen identiteit van de toepassing in plaats van gedelegeerde machtigingen. De verificatiestroom die in dit voorbeeld wordt gebruikt, staat bekend als de *[oauth-stroom voor clientreferenties](v2-oauth2-client-creds-grant-flow.md)* . Zie [dit artikel](scenario-daemon-overview.md) voor meer informatie over het gebruik van MSAL Python met daemonapps.

 U kunt MSAL Python installeren door de volgende pip-opdracht uit te voeren.

```powershell
pip install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```Python
import msal
```

Vervolgens initialiseert u MSAL met de volgende code:

```Python
app = msal.ConfidentialClientApplication(
    config["client_id"], authority=config["authority"],
    client_credential=config["secret"])
```

> | Waar: |Beschrijving |
> |---------|---------|
> | `config["secret"]` | Het clientgeheim is dat voor de toepassing in Azure-portal wordt gemaakt. |
> | `config["client_id"]` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |
> | `config["authority"]`    | Het STS-eindpunt voor de gebruiker voor verificatie. Dat is meestal `https://login.microsoftonline.com/{tenant}` voor de openbare cloud, waarbij {tenant} de naam van uw tenant of uw tenant-id is.|

Raadpleeg de [referentie documentatie voor voor `ConfidentialClientApplication` ](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)meer informatie.

### <a name="requesting-tokens"></a>Tokens aanvragen

Als u een token wilt aanvragen met behulp van de identiteit van de app, gebruikt u de `AcquireTokenForClient`-methode:

```Python
result = None
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

> |Waar:| Beschrijving |
> |---------|---------|
> | `config["scope"]` | De aangevraagde bereiken bevat. Voor vertrouwelijke clients moet dit de indeling gebruiken die lijkt op `{Application ID URI}/.default` om aan te geven dat de bereiken die worden aangevraagd, statisch zijn gedefinieerd in het app-object dat is ingesteld in de Azure Portal (voor Microsoft Graph, `{Application ID URI}` punten `https://graph.microsoft.com` ). Voor aangepaste web-Api's `{Application ID URI}` wordt gedefinieerd onder de sectie **een API beschikbaar** maken in **app-registraties** in azure Portal.|

Raadpleeg de [referentie documentatie voor voor `AcquireTokenForClient` ](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client)meer informatie.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over daemon-toepassingen de landings pagina van het scenario.

> [!div class="nextstepaction"]
> [Een daemon-app die web-API's aanroept](scenario-daemon-overview.md)
