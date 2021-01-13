---
title: 'Snelstart: Aanmelden met Microsoft toevoegen aan een Python-web-app | Azure'
titleSuffix: Microsoft identity platform
description: In deze snelstart leert u hoe een Python-web-app gebruikers kan aanmelden, een toegangstoken kan ophalen uit het Microsoft-identiteitsplatform en de Microsoft Graph-API aanroept.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2019
ms.author: abpati
ms.custom: aaddev, devx-track-python, scenarios:getting-started, languages:Python
ms.openlocfilehash: 4b6da5fce9c4ebac671b90a91b39053fcc4c4d2c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017303"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Quickstart: Aanmelden met Microsoft toevoegen aan een Python-webapp

In deze quickstart downloadt u een codevoorbeeld en voert u dit uit. Het codevoorbeeld laat zien hoe gebruikers kunnen worden aangemeld met een Python-webtoepassing, en een toegangstoken kunnen krijgen om de Microsoft Graph API aan te roepen. Gebruikers met een persoonlijk Microsoft-account of een account in een willekeurige Azure AD-organisatie (Azure Active Directory) kunnen zich aanmelden bij de toepassing.

Zie [Hoe het voorbeeld werkt](#how-the-sample-works) voor een illustratie.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) of [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Session](https://pypi.org/project/Flask-Session/), [aanvragen](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

> [!div renderon="docs"]
>
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
>
> U hebt twee opties voor het starten van de quickstart-toepassing: express (Optie 1) en handmatig (Optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens het codevoorbeeld
>
> 1. Ga naar de [Azure-portal - App-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/PythonQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
>
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>.
> 1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
> 1. Selecteer onder **Beheren** de optie **App-registraties** > **Nieuwe registratie**.
> 1. Voer een **Naam** in voor de toepassing, bijvoorbeeld `python-webapp`. Gebruikers van uw app kunnen de naam zien. U kunt deze later wijzigen.
> 1. Selecteer onder **Ondersteunde accounttypen** de optie **Accounts in een organisatieadreslijst en persoonlijke Microsoft-account**.
> 1. Selecteer **Registreren**.
> 1. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** voor later gebruik.
> 1. Selecteer **Verificatie** onder **Beheren**.
> 1. Selecteer **Een platform toevoegen** > **Web**.
> 1. Voeg `http://localhost:5000/getAToken` toe als **Omleidings-URI's**.
> 1. Selecteer **Configureren**.
> 1. Selecteer onder **Beheren** de optie **Certificaten en geheimen**, en in de sectie **Clientgeheimen** selecteert u **Nieuw clientgeheim**.
> 1. Typ een beschrijving voor de sleutel (bijvoorbeeld 'app-geheim'), laat de standaardvervaldatum staan, en selecteer **Toevoegen**.
> 1. Noteer de **Waarde** van het **Clientgeheim** voor later gebruik.
> 1. Selecteer onder **Beheren** achtereenvolgens **API-machtigingen** > **Een machtiging toevoegen**.
>1.  Zorg ervoor dat het tabblad **Microsoft-API's** is geselecteerd.
> 1. Selecteer in de sectie *Veelgebruikte Microsoft-API's* de optie **Microsoft Graph**.
> 1. Zorg ervoor dat in de sectie **Gedelegeerde toestemmingen** de juiste machtigingen zijn aangevinkt: **User.ReadBasic.All**. Gebruik het zoekvak indien nodig.
> 1. Selecteer de knop **Toestemmingen toevoegen**.
>
> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: uw toepassing configureren in Azure Portal
>
> Voor een juiste werking van het codevoorbeeld uit deze quickstart, moet u:
>
> 1. Een antwoord-URL toevoegen als `http://localhost:5000/getAToken`.
> 1. Een clientgeheim maken.
> 1. De gedelegeerde toestemming User.ReadBasic.All van Microsoft Graph API toevoegen.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-aspnet-webapp/green-check.png) Uw toepassing is al geconfigureerd met dit kenmerk

#### <a name="step-2-download-your-project"></a>Stap 2: uw project downloaden
> [!div renderon="docs"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Download het project en pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [Het codevoorbeeld downloaden](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-application"></a>Stap 3: De toepassing configureren
>
> 1. Pak het zip-bestand uit in een lokale map dichter bij de hoofdmap (bijvoorbeeld **C:\Azure-Samples**)
> 1. Als u een Integrated Development Environment gebruikt, opent u het voorbeeld in uw favoriete IDE (optioneel).
> 1. Open het bestand **app_config.py**, dat u kunt vinden in de hoofdmap en vervang dit door het volgende codefragment:
>
> ```python
> CLIENT_ID = "Enter_the_Application_Id_here"
> CLIENT_SECRET = "Enter_the_Client_Secret_Here"
> AUTHORITY = "https://login.microsoftonline.com/Enter_the_Tenant_Name_Here"
> ```
> Waar:
>
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Client_Secret_Here`: het **clientgeheim** dat u in **Certificaten en geheimen** hebt gemaakt voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Tenant_Name_Here`: de waarde voor **Map-id (tenant)** van de toepassing die u hebt geregistreerd.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Stap 3: Het codevoorbeeld uitvoeren

> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Stap 4: Het codevoorbeeld uitvoeren

1. U moet de MSAL Python-bibliotheek, Flask-framework, Flask-Sessions voor sessiebeheer aan de serverzijde en aanvragen installeren met behulp van PIP als volgt:

    ```Shell
    pip install -r requirements.txt
    ```

2. Voer app.py uit vanuit de shell of de opdrachtregel:

    ```Shell
    python app.py
    ```
   > [!IMPORTANT]
   > Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie voor meer informatie over het gebruik van een certificaat [deze instructies](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Meer informatie

### <a name="how-the-sample-works"></a>Hoe het voorbeeld werkt
![Toont hoe de voorbeeld-app werkt die is gegenereerd door deze quickstart](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>MSAL ophalen
MSAL is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en de aanvraagtokens die worden gebruikt voor toegang tot een API die is beveiligd via het Microsoft-identiteitsplatform.
U kunt MSAL Python met behulp van PIP toevoegen aan uw toepassing.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL initialiseren
U kunt de verwijzing toevoegen aan MSAL Python door de volgende code toe te voegen aan de bovenkant van het bestand waarin u MSAL gaat gebruiken:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over web-apps waarmee gebruikers worden aangemeld in onze meerdelige scenarioreeks.

> [!div class="nextstepaction"]
> [Scenario: Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)
