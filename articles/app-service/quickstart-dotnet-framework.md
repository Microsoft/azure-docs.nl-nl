---
title: 'Quickstart: Een C# ASP.NET-app maken'
description: Leer hoe u web-apps uitvoert in Azure App Service door de standaard C# ASP.NET-web-app vanuit Visual Studio te implementeren.
ms.assetid: 04a1becf-7756-4d4e-92d8-d9471c263d23
ms.topic: quickstart
ms.date: 11/20/2020
ms.custom: devx-track-csharp, mvc, devcenter, seodec18
ms.openlocfilehash: a4f7ba288bc27d6079deea9caf0ea315a55d0745
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106011189"
---
# <a name="create-an-aspnet-framework-web-app-in-azure"></a>Een ASP.NET Framework-web-app maken in Azure

[Azure App Service](overview.md) biedt een uiterst schaalbare webhostingservice met self-patchfunctie.

In deze snelstartgids ziet u hoe u uw eerste ASP.NET-web-app implementeert naar Azure App Service. Wanneer u klaar bent, hebt u een App Service-plan. U hebt ook een App Service-app met een geïmplementeerde webtoepassing.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

U moet <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> met de **ASP.NET- en webontwikkelworkload** installeren om deze zelfstudie te doorlopen.

Als u Visual Studio 2019 al hebt geïnstalleerd:

- Installeer de nieuwste updates in Visual Studio door **Help** > **Controleren op updates** te selecteren.
- Voeg de workload toe door **Hulpprogramma's** > **Hulpprogramma's en functies ophalen** te selecteren.

## <a name="create-an-aspnet-web-app"></a>Een ASP.NET-web-app maken<a name="create-and-publish-the-web-app"></a>

Maak een ASP.NET-web-app door de volgende stappen uit te voeren:

1. Open Visual Studio en selecteer **Een nieuw project maken**.

2. In **Een nieuw project maken** kiest u **ASP.NET-webtoepassing (.NET Framework)** en selecteert u **Volgende**.

3. Geef in **Uw nieuwe project configureren** de toepassing de naam _myfirstazurewebapp_ en selecteer **Maken**.

   ![Uw web-app-project configureren](./media/quickstart-dotnet-framework/configure-web-app-project-framework.png)

4. U kunt elk type ASP.NET-web-app implementeren in Azure. Kies voor deze quickstart de sjabloon **MVC**.

5. Zorg dat verificatie is ingesteld op **Geen verificatie**. Selecteer **Maken**.

   ![ASP.NET-webtoepassing maken](./media/quickstart-dotnet-framework/select-mvc-template-vs2019.png)

6. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![De app lokaal uitvoeren](./media/quickstart-dotnet-framework/local-web-app.png)

## <a name="publish-your-web-app"></a>Uw web-app publiceren <a name="launch-the-publish-wizard"></a>

1. Klik in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteer **Publiceren**.

1. Selecteer bij **Publiceren** de optie **Azure** en klik op **Volgende**.

1. Selecteer **Azure App Service (Windows)** en klik op **Volgende**.

   <!-- ![Publish from project overview page](./media/quickstart-dotnet-framework/publish-app-framework-vs2019.png) -->

1. Uw opties zijn afhankelijk van het feit of u al bent aangemeld bij Azure en of u een Visual Studio-account hebt gekoppeld aan een Azure-account. Selecteer **Een account toevoegen** of **Aanmelden** om u aan te melden bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account.

   ![Aanmelden bij Azure](./media/quickstart-dotnet-framework/sign-in-azure-framework-vs2019.png)

   [!INCLUDE [resource group intro text](../../includes/resource-group.md)]

1. Klik rechts van **App Service-exemplaren** op **+** .

   ![Nieuwe App Service-app](./media/quickstart-dotnet-framework/publish-new-app-service.png)

1. Selecteer in **Resourcegroep** de optie **Nieuw**.

1. Voer bij **Naam van nieuwe resourcegroep** in: *myResourceGroup*. Selecteer vervolgens **OK**.

   [!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. Selecteer bij **Hostingabonnement** de optie **Nieuw**.

1. Voer in het dialoogvenster **Hostingabonnement** de waarden uit de volgende tabel in en selecteer **OK**.

   | Instelling | Voorgestelde waarde | Beschrijving |
   |-|-|-|
   | Hostingabonnement| myAppServicePlan | De naam van het App Service-plan. |
   | Locatie | Europa -west | Het datacenter waar de web-app wordt gehost. |
   | Grootte | Gratis | De [prijscategorie](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) bepaalt de hosting-functies. |

   ![Een App Service-plan maken](./media/quickstart-dotnet-framework/app-service-plan-framework-vs2019.png)

1. Voer bij **Naam** een unieke app-naam in die alleen deze geldige tekens bevat: `a-z`, `A-Z`, `0-9` en `-`. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is `http://<app-name>.azurewebsites.net`, waarbij `<app-name>` de naam van uw app is.

2. Selecteer **Maken** om de Azure-resources te maken.

   ![Uw app-naam configureren](./media/quickstart-dotnet-framework/web-app-name-framework-vs2019.png)

    Zodra de wizard is voltooid, worden de Azure-resources gemaakt en bent u klaar om te publiceren.

3. Klik op **Voltooien** om de wizard te sluiten.

3. Klik op de pagina **Publiceren** op **Publiceren**. Visual Studio maakt, verpakt en publiceert de app naar Azure. Daarna wordt de app gestart in de standaardbrowser.

    ![Gepubliceerde ASP.NET-web-app in Azure](./media/quickstart-dotnet-framework/published-azure-web-app.png)

De naam van de app die is opgegeven op de pagina **Nieuwe App Service maken** wordt gebruikt als URL-voorvoegsel in de indeling `http://<app-name>.azurewebsites.net`.

**Gefeliciteerd!** Uw ASP.NET-web-app wordt live uitgevoerd in Azure App Service.

## <a name="update-the-app-and-redeploy"></a>De app bijwerken en opnieuw implementeren

1. In **Solution Explorer**, onder uw project, opent u **Weergaven** > **Start** > **Index.cshtml**.

1. Zoek ergens bovenaan de HTML-tag `<div class="jumbotron">` en vervang het volledige element door de volgende code:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**. Selecteer vervolgens **Publiceren**.

    Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

    ![Bijgewerkte ASP.NET-web-app in Azure](./media/quickstart-dotnet-framework/updated-azure-web-app.png)

## <a name="manage-the-azure-app"></a>De Azure-app beheren

1. Als u de web-app wilt beheren, gaat u naar de [Azure-portal](https://portal.azure.com) en selecteert u **App Services**.

   ![App Services selecteren](./media/quickstart-dotnet-framework/app-services.png)

2. Selecteer op de pagina **App Services** de naam van uw web-app.

   ![Navigatie naar Azure-app in de portal](./media/quickstart-dotnet-framework/access-portal-framework-vs2019.png)

   De pagina Overzicht van uw web-app wordt weergegeven. Hier kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen.

   ![App Service-overzicht in de Azure-portal](./media/quickstart-dotnet-framework/web-app-general-framework-vs2019.png)

   Het linkermenu bevat een aantal pagina's voor het configureren van uw app.

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [ASP.NET met SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

> [!div class="nextstepaction"]
> [ASP.NET configureren](configure-language-dotnet-framework.md)
