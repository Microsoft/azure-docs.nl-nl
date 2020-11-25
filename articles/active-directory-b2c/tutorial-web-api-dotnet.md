---
title: 'Zelfstudie: Toegang verlenen aan een ASP.NET-web-API'
titleSuffix: Azure AD B2C
description: Zelfstudie over het gebruik van Active Directory B2C om een ASP.NET web-API te beveiligen en aan te roepen vanuit een ASP.NET-webtoepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/14/2019
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: ada4226500437e7733605a29988ee7cacae40761
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994305"
---
# <a name="tutorial-grant-access-to-an-aspnet-web-api-using-azure-active-directory-b2c"></a>Zelfstudie: Toegang verlenen aan een web-API van ASP.NET met behulp van Azure Active Directory B2C

Deze zelfstudie laat zien hoe u een web-API-resource, die wordt beveiligd in Azure AD B2C (Active Directory B2C), aanroept vanuit een ASP.NET-webtoepassing.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen en de vereisten in de [zelfstudie: Verificatie inschakelen in een webtoepassing met behulp van Azure Active Directory B2C](tutorial-web-app-dotnet.md).

## <a name="add-a-web-api-application"></a>Een web-API-toepassing toevoegen

Web-API-resources moeten worden geregistreerd in uw tenant voordat deze in staat zijn om beveiligde resourceaanvragen door clienttoepassingen die een toegangstoken aanbieden, kunnen accepteren en erop kunnen reageren.

Als u een toepassing wilt registreren in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties** en selecteer vervolgens **Nieuwe registratie**.
1. Voer een **naam** in voor de toepassing. Bijvoorbeeld *webapi1*.
1. Selecteer onder **Omleidings-id** de optie **Web**, en voer vervolgens een eindpunt in waarop met Azure AD B2C tokens worden geretourneerd die zijn aangevraagd via de toepassing. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:44332`.
1. Selecteer **Registreren**.
1. Noteer de **Toepassings-id (client)** voor gebruik in een latere stap.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
3. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
4. Selecteer **Toepassingen (verouderd)** , en selecteer vervolgens **Toevoegen**.
5. Voer een naam in voor de toepassing. Bijvoorbeeld *webapi1*.
6. Selecteer **Ja** bij **Web-app / web-API opnemen**.
7. Voer voor de **Antwoord-URL** een eindpunt in waarop Azure AD B2C tokens retourneert die door uw toepassing worden aangevraagd. In deze zelfstudie wordt het voorbeeld lokaal uitgevoerd en luistert dit op `https://localhost:44332`.
8. Voer voor **App ID URI** de id in die wordt gebruikt voor uw web-API. De volledige id-URI, inclusief het domein, wordt voor u gegenereerd. Bijvoorbeeld `https://contosotenant.onmicrosoft.com/api`.
9. Klik op **Create**.
10. Noteer op de eigenschappenpagina de toepassings-ID die u gebruikt wanneer u de webtoepassing configureert.

* * *

## <a name="configure-scopes"></a>Bereiken configureren

Bereiken bieden een manier om toegang tot beveiligde resources te reguleren. Bereiken worden door de web-API gebruikt om toegangsbeheer op basis van een bereik te implementeren. Bijvoorbeeld: gebruikers van de web-API kunnen zowel lees- als schrijftoegang hebben of alleen leestoegang. In deze zelfstudie gebruikt u bereiken om lees- en schrijfmachtigingen voor de web-API te definiëren.

[!INCLUDE [active-directory-b2c-scopes](../../includes/active-directory-b2c-scopes.md)]

## <a name="grant-permissions"></a>Machtigingen verlenen

Als u een beveiligde web-API wilt aanroepen vanuit een app, moet u uw toepassing machtigingen verlenen tot de API. In de vereiste zelfstudie hebt u een webtoepassing in Azure AD B2C gemaakt met de naam *webapp1*. Met deze toepassing kunt u de web-API aanroepen.

[!INCLUDE [active-directory-b2c-permissions-api](../../includes/active-directory-b2c-permissions-api.md)]

Uw toepassing is geregistreerd voor het aanroepen van de beveiligde web-API. Een gebruiker voert een verificatie uit bij Azure AD B2C om de toepassing te kunnen gebruiken. De toepassing verkrijgt een autorisatietoekenning van Azure AD B2C voor toegang tot de beveiligde web-API.

## <a name="configure-the-sample"></a>Voorbeeld configureren

Nu de web-API is geregistreerd en u bereiken hebt gedefinieerd, moet u de web-API-code configureren om uw Azure AD B2C-tenant te gebruiken. In deze zelfstudie gaat u een voorbeeld-web-API configureren. De voorbeeld-web-API bevindt zich in het project dat u hebt gedownload in de vereiste zelfstudie.

Er bevinden zich twee projecten in de voorbeeldoplossing:

* **TaskWebApp**: een lijst met taken maken en bewerken. Het voorbeeld gebruikt de gebruikersstroom voor **registratie of aanmelding** om gebruikers te registreren of aan te melden.
* **TaskService**: biedt ondersteuning voor het maken, lezen, bijwerken en verwijderen van takenlijstfunctionaliteit. De API wordt beveiligd door Azure AD B2C en wordt aangeroepen door TaskWebApp.

### <a name="configure-the-web-application"></a>De web-app configureren

1. Open de oplossing **B2C-WebAPI-DotNet** in Visual Studio.
1. Open **Web.config** in het project **TaskWebApp**.
1. Als u de API lokaal wilt uitvoeren, moet u de localhost-instelling gebruiken voor **api:TaskServiceUrl**. Ga als volgt te werk om Web.config te wijzigen:

    ```csharp
    <add key="api:TaskServiceUrl" value="https://localhost:44332/"/>
    ```

1. Configureer de URI van de API. Dit is de URI die de web-app gebruikt om de API-aanvraag te maken. Configureer ook de machtigingen die zijn aangevraagd.

    ```csharp
    <add key="api:ApiIdentifier" value="https://<Your tenant name>.onmicrosoft.com/api/" />
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

### <a name="configure-the-web-api"></a>De web-API configureren

1. Open **Web.config** in het project **TaskService**.
1. Configureer de API om uw tenant te gebruiken.

    ```csharp
    <add key="ida:AadInstance" value="https://<Your tenant name>.b2clogin.com/{0}/{1}/v2.0/.well-known/openid-configuration" />
    <add key="ida:Tenant" value="<Your tenant name>.onmicrosoft.com" />
    ```

1. Stel de client-id in op de toepassings-id van uw geregistreerde web-API-toepassing *webapi1*.

    ```csharp
    <add key="ida:ClientId" value="<application-ID>"/>
    ```

1. Werk de instelling voor de gebruikersstroom bij met de naam van de gebruikersstroom voor registratie en aanmelding. *B2C_1_signupsignin1*.

    ```csharp
    <add key="ida:SignUpSignInPolicyId" value="B2C_1_signupsignin1" />
    ```

1. Configureer de bereikinstellingen zodat deze overeenkomen met wat u in de portal hebt gemaakt.

    ```csharp
    <add key="api:ReadScope" value="demo.read" />
    <add key="api:WriteScope" value="demo.write" />
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

U moet zowel project **TaskWebApp** als **TaskService** uitvoeren.

1. Klik in Solution Explorer met de rechtermuisknop op de oplossing en selecteer vervolgens **Opstartprojecten instellen...** .
1. Selecteer **Meerdere opstartprojecten**.
1. Wijzig de **actie** voor beide projecten in **Start**.
1. Klik op **OK** om de configuratie op te slaan.
1. Druk op **F5** om beide toepassingen uit te voeren. Elke toepassing wordt geopend in een apart browservenster.
    * `https://localhost:44316/` is de webtoepassing.
    * `https://localhost:44332/` is de web-API.

1. Selecteer in de webtoepassing de optie **Registreren / aanmelden** om u aan te melden bij de webtoepassing. Gebruik het account dat u eerder hebt gemaakt.
1. Nadat u bent aangemeld, selecteert u **Takenlijst** en maakt u een takenlijstitem.

Als u een takenlijstitem maakt, verzendt de webtoepassing een aanvraag naar de web-API om het takenlijstitem te maken. De beveiligde webtoepassing roept de web-API aan die is beveiligd met Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een web-API-toepassing toevoegen
> * Bereiken voor een web-API configureren
> * Machtigingen verlenen aan de web-API
> * Het voorbeeld configureren voor gebruik van de toepassing

> [!div class="nextstepaction"]
> [Zelfstudie: Id-providers toevoegen aan uw toepassingen in Azure Active Directory B2C](tutorial-add-identity-providers.md)