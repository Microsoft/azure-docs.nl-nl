---
title: 'Zelfstudie: Verificatie inschakelen in een Python-webtoepassing'
titleSuffix: Azure AD B2C
description: In deze zelfstudie leert u hoe u Azure Active Directory B2C gebruikt voor het opgeven van gebruikersaanmeldingsreferenties voor een Python Flask-webtoepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 06/12/2020
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.custom: devx-track-python
ms.openlocfilehash: 56e92a698446bd417c74820d6da662ad9ee55c77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555673"
---
# <a name="tutorial-enable-authentication-in-a-python-web-application-with-azure-ad-b2c"></a>Zelfstudie: Verificatie inschakelen in een Python-webtoepassing met Azure AD B2C

In deze zelfstudie leert u hoe u Azure Active Directory B2C (Azure AD B2C) kunt gebruiken voor het registreren en aanmelden van gebruikers in een Python Flask-toepassing.

In deze zelfstudie:

> [!div class="checklist"]
> * Een antwoord-URL toevoegen aan een toepassing die is geregistreerd in uw Azure AD B2C-Tenant
> * Een codevoorbeeld downloaden van GitHub
> * De code van de voorbeeldtoepassing wijzigen zodat deze met uw tenant werkt
> * Meld u aan met uw aanmeldings-/registratiegebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U hebt de volgende Azure AD B2C-resources nodig om door te gaan met de stappen in deze zelfstudie:

* [Azure AD B2C-tenant](tutorial-create-tenant.md)
* [Toepassing moet zijn geregistreerd](tutorial-register-applications.md) in uw tenant, met de bijbehorende *toepassings-id (client-id)* en het *clientgeheim*
* [Gebruikersstromen die zijn gemaakt](tutorial-create-user-flows.md) in uw tenant

Daarnaast hebt u het volgende nodig voor uw lokale ontwikkelomgeving:

* [Visual Studio Code](https://code.visualstudio.com/) of een andere code-editor
* [Python](https://nodejs.org/en/download/) 2.7+ of 3+

## <a name="add-a-redirect-uri"></a>Een omleidings-URI toevoegen

In de tweede zelfstudie die u als onderdeel van de vereisten hebt voltooid, hebt u een webtoepassing geregistreerd in Azure AD B2C. Als u communicatie wilt inschakelen voor het codevoorbeeld in deze zelfstudie, voegt u een antwoord-URL (ook wel een omleidings-URL genoemd) toe aan de registratie van de toepassing.

Als u een toepassing wilt bijwerken in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties**, selecteer het tabblad **Toepassingen in eigendom** en selecteer vervolgens de toepassing *webapp1*.
1. Selecteer **Verificatie** onder **Beheren**.
1. Selecteer onder **Web** de koppeling **URI toevoegen** en voer `http://localhost:5000/getAToken` in het tekstvak in.
1. Selecteer **Opslaan**.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure-portal en zoek vervolgens **Azure AD B2C** en selecteer deze.
1. Selecteer **Toepassingen (verouderd)** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`http://localhost:5000/getAToken` toe.
1. Selecteer **Opslaan**.
* * *

## <a name="get-the-sample-code"></a>De voorbeeldcode halen

In deze zelfstudie configureert u een codevoorbeeld dat u van GitHub downloadt om met uw B2C-tenant te werken. In het voorbeeld ziet u hoe een Python Flask-app Azure AD B2C kan gebruiken voor gebruikersregistratie en -aanmelding.

[Download een ZIP-archief](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip) of kloon de [opslagplaats met codevoorbeelden](https://github.com/Azure-Samples/ms-identity-python-webapp) vanuit GitHub.

```console
git clone https://github.com/Azure-Samples/ms-identity-python-webapp.git
```

## <a name="update-the-sample"></a>Het voorbeeld bijwerken

Wanneer u het voorbeeld hebt opgehaald, configureert u de toepassing voor het gebruik van uw Azure AD B2C-Tenant, toepassingsregistratie en gebruikersstromen.

In de hoofdmap van het project:

1. Wijzig de naam van het bestand *app_config.py* in *app_config.py.OLD*
1. Wijzig de naam van het bestand *app_config_b2c.py* in *app_config.py*

Werk het zojuist hernoemde bestand *app_config.py* bij met waarden voor uw Azure AD B2C-tenant-en toepassingsregistratie die u hebt gemaakt als onderdeel van de vereisten.

1. Open het bestand *app_config.py* in de editor.
1. Werk de waarde `b2c_tenant` bij met de naam van uw Azure AD B2C-tenant, bijvoorbeeld *contosob2c*.
1. Werk alle `*_user_flow`-waarden bij, zodat deze overeenkomen met de namen van de gebruikersstromen die u hebt gemaakt als onderdeel van de vereisten.
1. Werk de waarde `CLIENT_ID` bij met de **Toepassings-id (client-id)** van de toepassing die u hebt geregistreerd als onderdeel van de vereisten.
1. Werk de waarde `CLIENT_SECRET` bij met de waarde van het **clientgeheim** dat u hebt gemaakt in de vereisten. Voor een betere beveiliging kunt u overwegen om deze in plaats daarvan op te slaan in een **omgevingsvariabele**, zoals wordt aanbevolen in de opmerkingen.

Het bovenste gedeelte van *app_config.py* moet er nu uitzien zoals in het volgende codefragment:

```python
import os

b2c_tenant = "contosob2c"
signupsignin_user_flow = "B2C_1_signupsignin1"
editprofile_user_flow = "B2C_1_profileediting1"
resetpassword_user_flow = "B2C_1_passwordreset1"
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"

CLIENT_ID = "11111111-1111-1111-1111-111111111111" # Application (client) ID of app registration

CLIENT_SECRET = "22222222-2222-2222-2222-222222222222" # Placeholder - for use ONLY during testing.
# In a production app, we recommend you use a more secure method of storing your secret,
# like Azure Key Vault. Or, use an environment variable as described in Flask's documentation:
# https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
# CLIENT_SECRET = os.getenv("CLIENT_SECRET")
# if not CLIENT_SECRET:
#     raise ValueError("Need to define CLIENT_SECRET environment variable")
```

> [!WARNING]
> Zoals aangegeven in de opmerkingen over codefragmenten, raden we u aan **geen geheimen op te slaan in tekst zonder opmaak** in de code van uw toepassing. De in code vastgelegde variabele wordt in het codevoorbeeld alleen voor het *gemak* gebruikt. U kunt een omgevingsvariabele of een geheime opslag zoals Azure Key Vault gebruiken.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Ga in de console of terminal naar de map met het voorbeeld. Bijvoorbeeld:

    ```console
    cd ms-identity-python-webapp
    ```
1. Voer de volgende opdrachten uit om de vereiste pakketten van PyPi te installeren en de web-app op uw lokale computer uit te voeren:

    ```console
    pip install -r requirements.txt
    flask run --host localhost --port 5000
    ```

    In het consolevenster wordt het poortnummer van de lokaal uitgevoerde toepassing weergegeven:

    ```console
     * Serving Flask app "app" (lazy loading)
     * Environment: production
       WARNING: This is a development server. Do not use it in a production deployment.
       Use a production WSGI server instead.
     * Debug mode: off
     * Running on http://localhost:5000/ (Press CTRL+C to quit)
    ```

1. Blader naar `http://localhost:5000` om de webtoepassing weer te geven die op uw lokale machine wordt uitgevoerd.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-01.png" alt-text="Webbrowser met Python Flask-webtoepassing die lokaal wordt uitgevoerd":::

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

Deze voorbeeldtoepassing ondersteunt registreren, aanmelden en wachtwoorden opnieuw instellen. In deze zelfstudie meldt u zich aan met een e-mailadres.

1. Selecteer **Aanmelden** om de *B2C_1_signupsignin1*-gebruikersstroom te initiëren die u in een eerdere stap hebt opgegeven.
1. Azure AD B2C toont een aanmeldingspagina met een registratielink. Aangezien u nog geen account hebt, klikt u op de link **Nu registreren**.
1. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    :::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-02.png" alt-text="Registratiepagina die wordt weergegeven door de Azure AD B2C-gebruikersstroom":::

1. Selecteer **Maken** om een lokaal account te maken in de Azure AD B2C-directory.

Wanneer u **Maken** selecteert, wordt de naam van de aangemelde gebruiker weergegeven in de toepassing.

:::image type="content" source="media/tutorial-web-app-python/python-flask-web-app-03.png" alt-text="Webbrowser met Python Flask-webtoepassing met aangemelde gebruiker":::

Als u het aanmelden wilt testen, selecteert u achtereenvolgens de koppeling **Afmelden** en de optie **Aanmelden** en meldt u zich aan met het e-mailadres en het wachtwoord dat u hebt ingevoerd tijdens de geregistratie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Python Flask-webtoepassing geconfigureerd, zodat u met een gebruikersstroom in uw Azure AD B2C-tenant kunt werken om registratie en aanmelding mogelijk te maken. U hebt de volgende stappen uitgevoerd:

> [!div class="checklist"]
> * U hebt een antwoord-URL toegevoegd aan een toepassing die is geregistreerd in uw Azure AD B2C-tenant
> * U hebt een codevoorbeeld gedownload van GitHub
> * De voorbeeldcode van de toepassing is gewijzigd zodat deze met uw tenant werkt
> * U hebt zich aangemeld met uw aanmeldings-/registratiegebruikersstroom

Lees nu hoe u de gebruikersinterface kunt aanpassen van de gebruikersstroompagina's die voor uw gebruikers worden weergegeven door Azure AD B2C:

> [!div class="nextstepaction"]
> [De interface van gebruikers ervaringen aanpassen in Azure AD B2C >](customize-ui.md)