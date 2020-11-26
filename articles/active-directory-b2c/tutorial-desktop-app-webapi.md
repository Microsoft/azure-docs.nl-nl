---
title: 'Zelfstudie: Toegang verlenen aan een web-API van Node.js vanuit een bureaubladtoepassing'
description: Zelfstudie over het gebruik van Active Directory B2C om een web-API van Node.js te beveiligen en aan te roepen vanuit een .NET-desktop-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/12/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 8eca6297a704e1ef478c09baf227cf622d6890bb
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953148"
---
# <a name="tutorial-grant-access-to-a-nodejs-web-api-from-a-desktop-app-using-azure-active-directory-b2c"></a>Zelfstudie: Toegang verlenen aan een web-API van Node.js vanuit een desktop-app met behulp van Azure Active Directory B2C

Deze zelfstudie laat zien hoe u een web-API van Node.js aanroept die wordt beveiligd met Azure AD B2C (Active Directory B2C), vanuit een WPF-desktop-app (Windows Presentation Foundation).

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen en de vereisten in [Zelfstudie: Gebruikers verifiëren in een systeemeigen desktopclient](tutorial-desktop-app.md).

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

[!INCLUDE [active-directory-b2c-appreg-webapi](../../includes/active-directory-b2c-appreg-webapi.md)]

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Sommige gebruikers kunnen bijvoorbeeld zowel lees- als schrijftoegang hebben, terwijl andere gebruikers mogelijk alleen-lezen-machtigingen hebben. In deze zelfstudie gaat u lees- en schrijfmachtigingen definiëren voor de web-API.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

Noteer de waarde onder **BEREIKEN** voor het bereik `demo.read` voor gebruik in een latere stap wanneer u de bureaubladtoepassing configureert. De volledige bereikwaarde is vergelijkbaar met `https://contosob2c.onmicrosoft.com/api/demo.read`.

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een systeemeigen clienttoepassing, moet u de geregistreerde systeemeigen clienttoepassingsmachtigingen verlenen aan de web-API die u hebt geregistreerd in Azure AD B2C.

In de zelfstudie met vereisten hebt u een systeemeigen clienttoepassing geregistreerd met de naam *nativeapp1*. Met de volgende stappen wordt de registratie van de systeemeigen toepassing geconfigureerd bij de API-bereiken die u in de vorige sectie hebt gemaakt voor *webapi1*. Hierdoor kan de bureaubladtoepassing een toegangstoken verkrijgen uit Azure AD B2C die de web-API kan gebruiken om bereiktoegang te controleren en te bieden voor de bijbehorende resources. Later in de zelfstudie gaat u zowel de bureaubladtoepassing als de web-API-codevoorbeelden configureren en uitvoeren.

Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Selecteer **App-registraties** en selecteer vervolgens de systeemeigen clienttoepassing die toegang moet hebben tot de API. Bijvoorbeeld *nativeapp1*.
1. Selecteer onder **Beheren** de optie **API-machtigingen**.
1. Selecteer onder **Geconfigureerde machtigingen** de optie **Een machtiging toevoegen**.
1. Selecteer het tabblad **Mijn API's**.
1. Selecteer de API waarvoor aan de systeemeigen clienttoepassing toegang moet worden verleend. Bijvoorbeeld *webapi1*.
1. Vouw onder **Machtiging** de optie **demo** uit, en selecteer vervolgens de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld *demo.read* en *demo.write*.
1. Selecteer **Machtigingen toevoegen**. Wacht, zoals aangegeven, een paar minuten voordat u verdergaat met de volgende stap.
1. Selecteer **Beheerderstoestemming verlenen voor (naam van uw tenant)** .
1. Selecteer het momenteel aangemelde beheerdersaccount, of meld u aan met een account in de Azure AD B2C-tenant waaraan minstens de rol *Cloudtoepassingsbeheerder* is toegewezen.
1. Selecteer **Accepteren**.
1. Selecteer **Vernieuwen** en controleer vervolgens of voor beide bereiken 'Verleend voor...' wordt weergegeven onder **Status**. Het kan enkele minuten duren voordat de machtigingen zijn doorgegeven.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Selecteer **Toepassingen (verouderd)** , en selecteer vervolgens *nativeapp1*.
1. Selecteer **API-toegang** en vervolgens **Toevoegen**.
1. Selecteer *webapi1* in de vervolgkeuzelijst **API selecteren**.
1. Selecteer in de vervolgkeuzelijst **Bereiken selecteren** de bereiken die u eerder hebt gedefinieerd. Bijvoorbeeld *demo.read* en *demo.write*.
1. Selecteer **OK**.

* * *

Een gebruiker voert een verificatie uit met Azure AD B2C om de WPF-bureaubladtoepassing te kunnen gebruiken. De bureaubladtoepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-samples"></a>De voorbeelden configureren

Nu de web-API is geregistreerd, en bereiken en machtigingen zijn geconfigureerd, configureert u de bureaubladtoepassing en web-API-voorbeelden om de Azure AD B2C-tenant te gebruiken.

### <a name="update-the-desktop-application"></a>De bureaubladtoepassing bijwerken

In een vereiste voor dit artikel hebt u een [WPF-bureaubladtoepassing](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) gewijzigd om aanmelden met een gebruikersstroom in de Azure AD B2C-tenant mogelijk te maken. In deze sectie werkt u deze zelfde toepassing bij om de web-API *webapi1* te raadplegen die u eerder hebt geregistreerd.

1. Open de oplossing **active-directory-b2c-wpf** (`active-directory-b2c-wpf.sln`) in Visual Studio.
1. Open in het project **active-directory-b2c-wpf** het bestand *App.xaml.cs* en zoek de volgende variabeledefinities.
    1. Vervang de waarde van de variabele `ApiScopes` door de waarde die u eerder hebt genoteerd toen u het bereik **demo.read** definieerde.
    1. Vervang de waarde van de variabele `ApiEndpoint` door de **Omleidings-URI** die u eerder hebt genoteerd toen u de web-API registreerde (bijvoorbeeld *webapi1*) in de tenant.

    Hier volgt een voorbeeld:

    ```csharp
    public static string[] ApiScopes = { "https://contosob2c.onmicrosoft.com/api/demo.read" };
    public static string ApiEndpoint = "http://localhost:5000";
    ```

### <a name="get-and-update-the-nodejs-api-sample"></a>Het Node.js API-voorbeeld ophalen en bijwerken

Vervolgens haalt u het Node.js web API-codevoorbeeld op vanuit GitHub en configureert u het om de web-API te gebruiken die u hebt geregistreerd in de Azure AD B2C-tenant.

[Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

Het Node.js web-API-voorbeeld maakt gebruik van de bibliotheek Passport.js zodat Azure AD B2C aanroepen naar de API kan beveiligen.

1. Open het `index.js`-bestand.
1. Werk deze variabeledefinities bij met de volgende waarden. Wijzig `<web-API-application-ID>` in de **Toepassings-id (client)** van de web-API (*webapi1*) die u eerder hebt geregistreerd. Wijzig `<your-b2c-tenant>` in de naam van de Azure AD B2C-tenant.

    ```nodejs
    var clientID = "<web-API-application-ID>";
    var b2cDomainHost = "<your-b2c-tenant>.b2clogin.com";
    var tenantIdGuid = "<your-b2c-tenant>.onmicrosoft.com";
    var policyName = "B2C_1_signupsignin1";
    ```
1. Omdat u de API lokaal uitvoert, werkt u het pad in de route voor de methode GET bij naar `/` in plaats van de locatie `/hello` van de demo-app:

    ```nodejs
    app.get("/",
    ```

## <a name="run-the-samples"></a>De voorbeelden uitvoeren

### <a name="run-the-nodejs-web-api"></a>Voer de web-API van Node.js uit

1. Start een opdrachtprompt voor Node.js.
2. Ga naar de map met het Node.js-voorbeeld. Bijvoorbeeld `cd c:\active-directory-b2c-javascript-nodejs-webapi`
3. Voer de volgende opdrachten uit:
    ```console
    npm install && npm update
    ```
    ```console
    node index.js
    ```

### <a name="run-the-desktop-application"></a>De desktop-app uitvoeren

1. Open de oplossing **active directory-b2c wpf** in Visual Studio.
2. Druk op **F5** om de desktop-app uit te voeren.
3. Meld u aan met het e-mailadres en het wachtwoord dat wordt gebruikt in de zelfstudie [Verificatie van gebruikers met Azure Active Directory B2C in een desktop-app](tutorial-desktop-app.md).
4. Klik op de knop **API aanroepen**.

Met de bureaubladtoepassing wordt een aanvraag ingediend bij de lokaal uitgevoerde web-API, en bij verificatie van een geldig toegangstoken wordt de weergavenaam van de aangemelde gebruiker weergegeven.

![Weergavenaam zichtbaar in het bovenste deelvenster van de WPF-bureaubladtoepassing](./media/tutorial-desktop-app-webapi/desktop-app-01-post-api-call.png)

Met de bureaubladtoepassing, die is beveiligd met Azure AD B2C, wordt de lokaal uitgevoerde web-API aangeroepen die ook is beveiligd met Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Voorbeeld bijwerken voor gebruik van de toepassing

> [!div class="nextstepaction"]
> [Zelfstudie: Id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)