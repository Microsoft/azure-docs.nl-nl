---
title: 'Zelfstudie: Verificatie toevoegen aan een web-app in Azure App Service | Azure'
description: In deze zelfstudie leert u hoe u verificatie en autorisatie inschakelt voor een web-app die wordt uitgevoerd in Azure App Service. De toegang tot de web-app beperken tot gebruikers in uw organisatie.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 04/02/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: b17cb6906a37d2cab4383fac18400b35dc8adb2f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223175"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Zelfstudie: Verificatie toevoegen aan uw web-app die wordt uitgevoerd in Azure App Service

Ontdek hoe u verificatie inschakelt voor uw web-app die wordt uitgevoerd in Azure App Service, en de toegang beperkt tot gebruikers in uw organisatie.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagram waarin de aanmelding van de gebruiker wordt weergegeven." border="false":::

App Service biedt ingebouwde ondersteuning voor verificatie en autorisatie, zodat u gebruikers kunt aanmelden en toegang tot gegevens kunt krijgen door minimale of helemaal geen code te schrijven in uw web-app. Het is niet verplicht de module App Service-verificatie/-autorisatie te gebruiken, maar het helpt wel de verificatie en autorisatie voor uw app te vereenvoudigen. In dit artikel wordt uitgelegd hoe u uw web-app beveiligt met de module App Service-verificatie/-autorisatie door Azure Active Directory (Azure AD) als de id-provider te gebruiken.

De verificatie-/autorisatiemodule wordt ingeschakeld en geconfigureerd via de Azure-portal en app-instellingen. Er zijn geen SDK's, specifieke talen of wijzigingen in de toepassingscode nodig. Diverse id-providers worden ondersteund, zoals Azure AD, Microsoft Account, Facebook, Google en Twitter. Wanneer de verificatie-/autorisatiemodule is ingeschakeld, gaat elke inkomende HTTP-aanvraag via de module voordat de aanvraag wordt verwerkt door app-code. Zie [Verificatie en autorisatie in Azure App Service](overview-authentication-authorization.md) voor meer informatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Verificatie configureren voor de web-app.
> * De toegang tot de web-app beperken tot gebruikers in uw organisatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Een web-app maken en publiceren in App Service

Voor deze zelfstudie moet er een web-app zijn geïmplementeerd in App Service. U kunt een bestaande web-app gebruiken of u kunt de [snelstartgids voor ASP.NET Core](quickstart-dotnetcore.md) volgen om een nieuwe web-app te maken en publiceren in App Service.

Of u nu een bestaande web-app gebruikt of een nieuwe maakt, noteer de naam van de web-app en de naam van de resourcegroep waarin de web-app is geïmplementeerd. U hebt deze namen nodig in deze zelfstudie. 

## <a name="configure-authentication-and-authorization"></a>Verificatie en autorisatie configureren

U hebt nu een web-app die wordt uitgevoerd in App Service. Vervolgens schakelt u verificatie en autorisatie voor de web-app in. U gebruikt Azure AD als de identiteitsprovider. Zie [Verificatie van Azure AD configureren voor de App Service-toepassing](configure-authentication-provider-aad.md) voor meer informatie.

Selecteer in het menu van [Azure Portal](https://portal.azure.com) de optie **Resourcegroepen** of zoek ernaar en selecteer **Resourcegroepen** vanaf een willekeurige pagina.

Zoek in **Resourcegroepen** uw resourcegroep en selecteer deze. Selecteer in **Overzicht** de beheerpagina van uw app.

:::image type="content" alt-text="Schermopname die het selecteren van de beheerpagina van uw app weergeeft." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

Selecteer in het menu links van uw app de optie **verificatie** en klik vervolgens op **ID-provider toevoegen**.

Selecteer op de pagina **een id-provider toevoegen** de optie **micro soft** als **ID-provider** om u aan te melden bij micro soft en Azure AD-identiteiten.

  >  Selecteer **nieuwe app-registratie maken** voor het **registratie type** van de app-registratie.

  >  Selecteer **huidige Tenant-enkelvoudige Tenant** voor de **ondersteunde account typen** voor app-registratie.

Laat in de sectie **verificatie-instellingen voor app service** dat verificatie **is** ingesteld op verificatie en **niet-geverifieerde aanvragen** die zijn ingesteld op **HTTP 302, omleiding zijn gevonden: aanbevolen voor websites**. 

Klik onder aan de pagina **een id-provider toevoegen** op **toevoegen** om verificatie voor uw web-app in te scha kelen.

:::image type="content" alt-text="Scherm afbeelding die de configuratie van de verificatie weergeeft." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

U hebt nu een app die wordt beveiligd door App Service-verificatie en -autorisatie.

## <a name="verify-limited-access-to-the-web-app"></a>Beperkte toegang tot de web-app verifiëren

Toen u de module App Service-verificatie/-autorisatie inschakelde, werd er een app-registratie gemaakt in uw Azure AD-tenant. De app-registratie heeft dezelfde weergavenaam als uw web-app. Om de instellingen te controleren, selecteert u **Azure Active Directory** in het portalmenu en selecteert u vervolgens **App-registraties**. Selecteer de app-registratie die is gemaakt. Verifieer in het overzicht dat **Ondersteunde accounttypen** is ingesteld op **Alleen mijn organisatie**.

:::image type="content" alt-text="Schermopname waarop de toegang wordt gecontroleerd." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

Om te verifiëren dat de toegang tot uw app is beperkt tot gebruikers in uw organisatie, start u een browser in incognito- of privémodus en gaat u naar `https://<app-name>.azurewebsites.net`. U wordt naar een beveiligde aanmeldingspagina geleid, wat verifieert dat niet-geverifieerde gebruikers geen toegang tot de site hebben. Meld u aan als gebruiker in uw organisatie om toegang tot de site te krijgen. U kunt ook een nieuwe browser starten en zich proberen aan te melden met behulp van een persoonlijk account om te verifiëren dat gebruikers buiten de organisatie geen toegang hebben.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met deze zelfstudie en de web-app of bijbehorende resources niet meer nodig hebt, kunt u [de gemaakte resources opschonen](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
>
> * Verificatie configureren voor de web-app.
> * De toegang tot de web-app beperken tot gebruikers in uw organisatie.

> [!div class="nextstepaction"]
> [App Service krijgt toegang tot opslag](scenario-secure-app-access-storage.md)
