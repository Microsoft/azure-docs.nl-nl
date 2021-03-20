---
title: 'Zelfstudie: Verificatie inschakelen in een webtoepassing'
titleSuffix: Azure AD B2C
description: Zelfstudie over het gebruik van Azure Active Directory B2C voor het opgeven van gebruikersaanmelding voor een ASP.NET-webtoepassing.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c3c63b6116e02e8a742b69e90c11e182d72ab2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953024"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>Zelfstudie: Verificatie inschakelen in een webtoepassing met behulp van Azure Active Directory B2C

Deze zelfstudie laat u zien hoe u Azure AD B2C (Active Directory B2C) kunt gebruiken voor het aanmelden en registreren van gebruikers in een ASP.NET-webtoepassing. Met Azure AD B2C kunnen uw toepassingen zich met behulp van open-standaardprotocollen verifiëren bij sociale accounts, Enterprise-accounts en Azure Active Directory-accounts.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De toepassing bijwerken in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> In deze zelfstudie wordt een voorbeeld van een ASP.NET-webtoepassing gebruikt. Zie voor andere voorbeeldtoepassingen (inclusief ASP.NET Core, Node.js, Python en meer) [Azure Active Directory B2C-codevoorbeelden](code-samples.md).

## <a name="prerequisites"></a>Vereisten

* [Maak gebruikersstromen](tutorial-create-user-flows.md) om gebruikerservaringen in uw toepassing in te schakelen.
* U moet [Visual Studio 2019](https://www.visualstudio.com/downloads/) met de **ASP.NET- en webontwikkelworkload** installeren.

## <a name="update-the-application-registration"></a>De toepassingsregistratie bijwerken

In de zelfstudie die u als onderdeel van de vereisten hebt voltooid, hebt u een webtoepassing geregistreerd in Azure AD B2C. Om te communiceren met het voorbeeld in deze zelfstudie moet u een omleidings-URI toevoegen en een clientgeheim (sleutel) maken voor de geregistreerde toepassing.

### <a name="add-a-redirect-uri-reply-url"></a>Een omleidings-URI (antwoord-URL) toevoegen

Als u een toepassing wilt bijwerken in de Azure AD B2C-tenant, kunt u de nieuwe uniforme ervaring voor **App-registraties** of de verouderde ervaring **Toepassingen (verouderd)** gebruiken. [Meer informatie over de nieuwe ervaring](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[App-registraties](#tab/app-reg-ga/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer het filter **Map + Abonnement** in het bovenste menu en selecteer vervolgens de map die uw Azure AD B2C-tenant bevat.
1. Selecteer **Azure AD B2C** in het linkermenu. Of selecteer **Alle services** en zoek naar en selecteer **Azure AD B2C**.
1. Selecteer **App-registraties**, selecteer het tabblad **Toepassingen in eigendom** en selecteer vervolgens de toepassing *webapp1*.
1. Selecteer onder **Web** de koppeling **URL toevoegen**, voer `https://localhost:44316` in, en selecteer vervolgens **Opslaan**.
1. Selecteer **Overzicht**.
1. Noteer de **Toepassings-id (client)** voor gebruik in een latere stap wanneer u de webtoepassing configureert.

#### <a name="applications-legacy"></a>[Toepassingen (verouderd)](#tab/applications-legacy/)

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zorg ervoor dat u de map gebruikt die uw Azure AD B2C-tenant bevat door in het bovenste menu te klikken op het filter **Map en abonnement** en de map te kiezen waarin de tenant zich bevindt.
1. Kies **Alle services** linksboven in de Azure Portal, zoek **Azure AD B2C** en selecteer deze.
1. Selecteer **Toepassingen (verouderd)** en selecteer vervolgens de toepassing *webapp1*.
1. Voeg onder **Antwoord-URL**`https://localhost:44316` toe.
1. Selecteer **Opslaan**.
1. Noteer op de eigenschappenpagina de toepassings-id voor gebruik in een latere stap wanneer u de webtoepassing configureert.

* * *

### <a name="create-a-client-secret"></a>Een clientgeheim maken

Maak vervolgens een clientgeheim voor de geregistreerde webtoepassing. Dit wordt in het codevoorbeeld van de webtoepassing gebruikt om de bijbehorende identiteit te bewijzen bij het aanvragen van tokens.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>Voorbeeld configureren

In deze zelfstudie configureert u een voorbeeld dat u kunt downloaden uit GitHub. Het voorbeeld maakt gebruik van ASP.NET voor een eenvoudige takenlijst. In het voorbeeld worden [Microsoft OWIN-middlewareonderdelen](/aspnet/aspnet/overview/owin-and-katana/) gebruikt. [Download een ZIP-bestand ](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip) of kloon de voorbeeld-web-app vanuit GitHub. Zorg ervoor dat u het voorbeeldbestand uitpakt in een map en dat het aantal tekens van het pad minder is dan 260.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

Deze twee projecten bevinden zich in de voorbeeldoplossing:

* **TaskWebApp**: een lijst met taken maken en bewerken. Het voorbeeld gebruikt de gebruikersstroom voor **registratie of aanmelding** om gebruikers te registreren en aan te melden.
* **TaskService**: biedt ondersteuning voor het maken, lezen, bijwerken en verwijderen van takenlijstfunctionaliteit. De API wordt beveiligd door Azure AD B2C en wordt aangeroepen door TaskWebApp.

U moet het voorbeeld wijzigen om de toepassing te gebruiken die geregistreerd staat in uw tenant, met inbegrip van de toepassings-ID en de sleutel die u eerder hebt genoteerd. U moet de gebruikersstromen die u hebt gemaakt ook configureren. Het voorbeeld definieert de configuratiewaarden als instellingen in het bestand *Web.config*.

Werk de instellingen in het bestand Web.config bij zodat ze werken met uw gebruikersstroom:

1. Open de oplossing **B2C-WebAPI-DotNet** in Visual Studio.
1. Open in het project **TaskWebApp** het bestand **Web.config**.
    1. Werk de waarde van `ida:Tenant` en `ida:AadInstance` bij met de naam van de Azure AD B2C-tenant die u hebt gemaakt. Vervang bijvoorbeeld `fabrikamb2c` door `contoso`.
    1. Vervang de waarde van `ida:TenantId` door de map-id. Deze vindt u in de eigenschappen van de Azure B2C-tenant (in de Azure-portal onder **Azure Active Directory** > **Eigenschappen** > **Map-id**).
    1. Vervang de waarde voor `ida:ClientId` door de toepassings-id die u hebt geregistreerd.
    1. Vervang de waarde voor `ida:ClientSecret` door de sleutel die u hebt geregistreerd. Als het clientgeheim een vooraf gedefinieerde XML-entiteit bevat, bijvoorbeeld een kleiner dan-teken (`<`), groter dan-teken (`>`), en-teken (`&`) of dubbele aanhalingsteken (`"`), moet u deze tekens uitsluiten door XML-codering toe te passen op het clientgeheim vóórdat u het toevoegt aan web.config.
    1. Vervang de waarde voor `ida:SignUpSignInPolicyId` door `b2c_1_signupsignin1`.
    1. Vervang de waarde voor `ida:EditProfilePolicyId` door `b2c_1_profileediting1`.
    1. Vervang de waarde voor `ida:ResetPasswordPolicyId` door `b2c_1_passwordreset1`.

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

1. Klik in Solution Explorer met de rechtermuisknop op het project **TaskWebApp** en klik op **Instellen als opstartproject**.
1. Druk op **F5**. De standaardbrowser wordt gestart met het adres van de lokale site `https://localhost:44316/`.

### <a name="sign-up-using-an-email-address"></a>Aanmelden met een e-mailadres

1. Selecteer **Registreren / aanmelden** om u te registreren als een gebruiker van de toepassing. De gebruikersstroom **b2c_1_signupsignin1** wordt gebruikt.
1. Azure AD B2C toont een aanmeldingspagina met een koppeling voor registratie. Aangezien u nog geen account hebt, klikt u op **Nu registreren**. Tijdens de aanmeldingswerkstroom wordt een pagina weergegeven waarmee de identiteit wordt opgehaald en gecontroleerd van de gebruiker die een e-mailadres heeft gebruikt. Tijdens de aanmeldingswerkstroom worden ook het wachtwoord van de gebruiker en de aangevraagde kenmerken opgehaald die in de gebruikersstroom zijn gedefinieerd.
1. Gebruik een geldig e-mailadres en voer de verificatie uit met de verificatiecode. Stel een wachtwoord in. Geef waarden voor de aangevraagde kenmerken op.

    ![Registratiepagina weergegeven als onderdeel van de werkstroom voor aanmelden/registreren](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. Selecteer **Maken** als u een lokaal account wilt maken in de Azure AD B2C-tenant.

Gebruikers van de toepassing kunnen nu hun e-mailadres gebruiken om zich aan te melden en de webtoepassing te gebruiken.

De **Takenlijst** werkt echter pas als u de volgende zelfstudie in de reeks hebt voltooid, [Zelfstudie: Azure AD B2C gebruiken om een ASP.NET-web-API te beveiligen](tutorial-web-api-dotnet.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Bijwerken van de toepassing in Azure AD B2C
> * Het voorbeeld configureren voor gebruik van de toepassing
> * Aanmelden met behulp van de gebruikersstroom

Ga nu verder met de volgende zelfstudie om de functie **Takenlijst** functie van de webtoepassing in te schakelen. In deze zelfstudie registreert u een web-API-toepassing in uw eigen Azure AD B2C-tenant, en wijzigt u vervolgens het codevoorbeeld om uw tenant te gebruiken voor API-verificatie.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Active Directory B2C gebruiken voor het beveiligen van een ASP.NET-web-API >](tutorial-web-api-dotnet.md)