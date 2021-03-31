---
title: 'Zelfstudie: Gebruikers verifiëren in een systeemeigen clienttoepassing'
titleSuffix: Azure AD B2C
description: Zelfstudie over het gebruik van Azure Active Directory B2C voor het opgeven van gebruikersaanmeldingsreferenties voor een .NETdesktop-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 06d27c3a3daa4702653a2063d0ac70fd094e2d74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78186196"
---
# <a name="tutorial-authenticate-users-in-a-native-desktop-client-using-azure-active-directory-b2c"></a>Zelfstudie: Gebruikers verifiëren in een systeem eigen clienttoepassing met behulp van Azure Active Directory B2C

Deze zelfstudie laat u zien hoe u Azure Active Directory B2C (Azure AD B2C) kunt gebruiken voor het aanmelden en registreren van gebruikers in een Windows Presentation Foundation-desktoptoepassing (WPF). Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De systeemeigen clienttoepassing toevoegen
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- [Maak gebruikersstromen](tutorial-create-user-flows.md) om gebruikerservaringen in uw toepassing in te schakelen.
- Installeer [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de workloads **.NET-desktopontwikkeling** en **ASP.NET- en webontwikkeling**.

## <a name="add-the-native-client-application"></a>De systeemeigen clienttoepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Noteer de **Toepassings-id (client)** voor gebruik in een latere stap.

## <a name="configure-the-sample"></a>Voorbeeld configureren

In deze zelfstudie configureert u een voorbeeld dat u kunt downloaden uit GitHub. Het voorbeeld van een WPF-desktoptoepassing laat registratie en aanmelding zien, en kan een beveiligde web-API in Azure AD B2C aanroepen. [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop/archive/msalv3.zip), [blader door de opslagplaats](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) of kloon de voorbeeldweb-app vanuit GitHub.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop.git
```

Als u de toepassing wilt bijwerken voor gebruik met uw Azure AD B2C-tenant en de gebruikersstromen wilt aanroepen in plaats van die in de standaarddemo-tenant:

1. Open de oplossing **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) in Visual Studio.
2. Open in het project **active-directory-b2c-wpf** het bestand *App.xaml.cs* en zoek de volgende variabeledefinities. Vervang `{your-tenant-name}` door de naam van de Azure AD B2C-tenant en `{application-ID}` door de toepassings-id die u eerder hebt vastgelegd.

    ```csharp
    private static readonly string Tenant = "{your-tenant-name}.onmicrosoft.com";
    private static readonly string AzureAdB2CHostname = "{your-tenant-name}.b2clogin.com";
    private static readonly string ClientId = "{application-ID}";
    ```

3. Werk de variabelen van de beleidsnaam bij met de namen van de gebruikersstromen die u hebt gemaakt als onderdeel van de vereisten. Bijvoorbeeld:

    ```csharp
    public static string PolicySignUpSignIn = "B2C_1_signupsignin1";
    public static string PolicyEditProfile = "B2C_1_profileediting1";
    public static string PolicyResetPassword = "B2C_1_passwordreset1";
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Druk op **F5** om het voorbeeld te bouwen en uit te voeren.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

1. Selecteer **Aanmelden** om u als een gebruiker aan te melden. Hiervoor wordt de gebruikersstroom **B2C_1_signupsignin1** gebruikt.
2. Azure AD B2C toont een aanmeldingspagina met de koppeling **Nu registreren**. Aangezien u nog geen account hebt, klikt u op de link **Nu registreren**.
3. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.

    Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![Registratiepagina weergegeven als onderdeel van de werkstroom voor aanmelden/registreren](./media/tutorial-desktop-app/azure-ad-b2c-sign-up-workflow.png)

4. Selecteer **Maken** als u een lokaal account wilt maken in de Azure AD B2C-tenant.

Gebruikers kunnen nu hun e-mailadres gebruiken om zich aan te melden en de desktoptoepassing te gebruiken. Na het registreren of aanmelden, worden de tokendetails weergegeven in het onderste deelvenster van de WPF-app.

![Tokendetails in het onderste deelvenster van de WPF-desktoptoepassing](./media/tutorial-desktop-app/desktop-app-01-post-signin.png)

Als u de knop **Aanroep-API** selecteert, wordt er een **foutbericht** weergegeven. U krijgt de fout omdat de toepassing in de huidige status toegang wil hebben tot een API die wordt beveiligd door de demo-tenant, `fabrikamb2c.onmicrosoft.com`. De API-aanroep wordt niet geautoriseerd omdat uw toegangstoken alleen geldig is voor uw Azure AD B2C-tenant.

Ga door naar de volgende zelfstudie om een beveiligde web-API in uw eigen tenant te registreren en de functionaliteit van de **Aanroep-API** in te schakelen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * De systeemeigen clienttoepassing toevoegen
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Geef vervolgens de WPF-desktoptoepassing toegang tot een web-API die is geregistreerd in uw eigen Azure AD B2C-tenant om de functionaliteit van de knop **Aanroep-API** in te schakelen:

> [!div class="nextstepaction"]
> [Zelfstudie: Toegang verlenen aan een web-API van Node.js vanuit een desktoptoepassing >](tutorial-desktop-app-webapi.md)
