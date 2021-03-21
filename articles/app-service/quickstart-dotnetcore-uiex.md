---
title: 'Quickstart: Een C# ASP.NET Core-app maken'
description: Leer hoe u web-apps uitvoert in Azure App Service door uw eerste ASP.NET Core-app te implementeren.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2a789b4ca1261c79e8e6eb93a4ed44e7e8e9272e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102214232"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Een ASP.NET Core-web-app maken in Azure

::: zone pivot="platform-windows"  

In deze Quick Start leert u hoe u uw eerste ASP.NET Core-Web-App maakt en implementeert om <abbr title="Een HTTP-gebaseerde service voor het hosten van webtoepassingen, REST-Api's en mobiele back-end-toepassingen.">Azure App Service</abbr>. App Service ondersteunt .NET 5.0-apps.

Wanneer u klaar bent, hebt u een Azure <abbr title="Een logische container voor gerelateerde Azure-resources die u kunt beheren als een eenheid.">resourcegroep</abbr>, bestaande uit een <abbr title="Het plan dat de locatie, grootte en functies opgeeft van de webserver farm die als host fungeert voor uw app.">App Service-plan</abbr> en een <abbr title="De weer gave van uw web-app, die uw app-code, DNS-hostnamen, certificaten en gerelateerde resources bevat.">App Service-app</abbr> met een geïmplementeerde voorbeeld toepassing ASP.NET Core.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

- **Een Azure-account ophalen** met een actief <abbr title="De basis organisatie structuur waarin u resources in azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- **Installeer <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>** met de **ASP.net-en Web Development** -werk belasting.

<details>
<summary>Hebt u Visual Studio 2019 al?</summary>
Als u Visual Studio 2019 al hebt geïnstalleerd:

<ul>
<li><strong>Installeer de meest recente updates</strong> in Visual Studio door <strong>Help</strong> &gt; <strong>controleren op updates</strong>te selecteren. De meest recente updates bevatten de .NET 5,0 SDK.</li>
<li><strong>Voeg de werk belasting toe</strong> door <strong>extra</strong> hulp middelen &gt; <strong>en functies</strong>te selecteren.</li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. een ASP.NET Core-web-app maken

1. Open Visual Studio en selecteer **Een nieuw project maken**.

1. Selecteer in **Een nieuw project maken** de **ASP.NET Core-webtoepassing**, en controleer of **C#** wordt vermeld in de talen voor deze keuze. Selecteer vervolgens **Volgende**.

1. Geef in **Uw nieuwe project configureren** het webtoepassingsproject de naam *myFirstAzureWebApp*, en selecteer **Maken**.

   ![Uw web-app-project configureren](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Voor een .NET 5.0-app selecteert u **ASP.NET Core 5.0** in de vervolgkeuzelijst. Gebruik anders de standaardwaarde.

1. U kunt elk type ASP.NET Core-web-app implementeren in Azure, maar voor deze quickstart kiest u de sjabloon **ASP.NET Core Web App**. Zorg ervoor dat **Verificatie** is ingesteld op **Geen verificatie** en dat er geen andere optie is geselecteerd. Ten slotte selecteert u **Create**.

   ![Een nieuw ASP.NET Core-web-app maken](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Selecteer in het menu van Visual Studio de optie **Foutopsporing** > **Starten zonder foutopsporing** om de web-app lokaal uit te voeren.

   ![Web-app die lokaal wordt uitgevoerd](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. uw web-app publiceren

1. Klik in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteer **Publiceren**. 

1. Selecteer bij **Publiceren** de optie **Azure** en klik op **Volgende**.

1. Uw opties zijn afhankelijk van het feit of u al bent aangemeld bij Azure en of u een Visual Studio-account hebt gekoppeld aan een Azure-account. Selecteer **Een account toevoegen** of **Aanmelden** om u aan te melden bij uw Azure-abonnement. Als u al bent aangemeld, selecteert u het gewenste account.

   ![Aanmelden bij Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. Klik rechts van **App Service-exemplaren** op **+** .

   ![Nieuwe App Service-app](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Accepteer bij **Abonnement** het abonnement dat wordt vermeld, of selecteer een nieuw abonnement in de vervolgkeuzelijst.

1. Selecteer in **Resourcegroep** de optie **Nieuw**. Voer bij **Naam van nieuwe resourcegroep** in: *myResourceGroup*. Selecteer vervolgens **OK**. 

1. Selecteer bij **Hostingabonnement** de optie **Nieuw**. 

1. Voer in het dialoogvenster **Hostingabonnement: Nieuw hostingabonnement maken** de waarden in die zijn opgegeven in de volgende tabel:

   | Instelling  | Voorgestelde waarde |
   | -------- | --------------- |
   | **Hostingabonnement**  | *myFirstAzureWebAppPlan* |
   | **Locatie**      | *Europa -west* |
   | **Grootte**          | *Gratis* |
   
   ![Nieuw hostingabonnement maken](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Voer bij **naam** een unieke app-naam in.

    <details>
        <summary>Welke tekens kan ik gebruiken?</summary>
        Geldige tekens zijn a-z, A-Z, 0-9 en-. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , waarbij <code>&lt;app-name&gt;</code> de naam van uw app is.
    </details>

1. Selecteer **Maken** om de Azure-resources te maken. 

   ![App-resources maken](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Wacht tot de wizard klaar is met het maken van Azure-resources. Klik op **Voltooien** om de wizard te sluiten.

1. Klik op de pagina **publiceren** op **publiceren** om uw project te implementeren. 

    <details>
        <summary>Wat doet Visual Studio?</summary>
        Visual Studio maakt, verpakt en publiceert de app naar Azure. Daarna wordt de app gestart in de standaardbrowser.
    </details>

   ![Gepubliceerde ASP.NET-web-app, uitgevoerd in Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. de app bijwerken en opnieuw implementeren

1. Open in **Solution Explorer**, onder uw project, achtereenvolgens **Pagina’s** > **Index.cshtml**.

1. Vervang de hele tag `<div>` door de volgende code:

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Als u opnieuw wilt implementeren naar Azure, klikt u in **Solution Explorer** met de rechtermuisknop op het project **myFirstAzureWebApp** en selecteert u **Publiceren**.

1. Selecteer op de samenvattingspagina **Publiceren** de optie **Publiceren**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Als het publiceren is voltooid, start Visual Studio een browser waarin de URL van de web-app wordt geladen.

    ![Bijgewerkte ASP.NET-web-app, uitgevoerd in Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. de Azure-app beheren

1. Ga naar het [Azure Portal](https://portal.azure.com)en zoek en selecteer **app Services**.

    ![App Services selecteren](./media/quickstart-dotnetcore/app-services.png)
    
1. Selecteer op de pagina **App Services** de naam van uw web-app.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Schermopname van de App Services-pagina waarop een voorbeeldweb-app is geselecteerd.":::

1. De **Overzichtspagina** voor de web-app bevat opties voor basisbeheer, zoals browsen, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat een meer pagina's voor het configureren van uw app.

    ![App Service in de Azure-portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. resources opschonen

1. Selecteer **Resourcegroepen** in het menu of op de **beginpagina** van de Azure-portal. Selecteer **myResourceGroup** op de pagina **Resourcegroepen**.

1. Controleer op de pagina **myResourceGroup** of de weergegeven resources de resources zijn die u wilt verwijderen.

1. Selecteer **Resourcegroep verwijderen**, typ **myResourceGroup** in het tekstvak om dit te bevestigen en selecteer **Verwijderen**.

<hr/> 

## <a name="next-steps"></a>Volgende stappen

Ga door met het volgende artikel om te leren hoe u een .NET Core-app maakt en deze verbindt met een SQL-database:

- [ASP.NET Core met SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [ASP.NET Core-app configureren](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
In deze Quick start ziet u hoe u een [.net core](/aspnet/core/) -app maakt op <abbr title="App Service onder Linux biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem.">App Service op Linux</abbr>. U maakt de app via de [Azure CLI](/cli/azure/get-started-with-azure-cli) en gebruikt Git om de .NET Core-code in de app te implementeren.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. uw omgeving voorbereiden

- **Een Azure-account** met een actief abonnement ophalen. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- **Installeer** de nieuwste <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.net Core 3,1 sdk</a> of <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.net 5,0 SDK</a>.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Installeer de nieuwste Azure cli</a>**.

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. de app lokaal maken

1. Voer uit `mkdir hellodotnetcore` om de map te maken.

    ```bash
    mkdir hellodotnetcore
    ```

1. Voer uit `cd hellodotnetcore` om de map te wijzigen. 

    ```bash
    cd hellodotnetcore
    ```

1. Voer uit `dotnet new web` om een nieuwe .net core-app te maken.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Voer de app lokaal uit

1. Voer uit `dotnet run` om te zien hoe het eruitziet wanneer u het implementeert in Azure.

    ```bash
    dotnet run
    ```
    
1. **Open een webbrowser** en navigeer naar de app op `http://localhost:5000` .

![Testen met browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Meld u aan bij Azure

Voer uit `az login` om aan te melden bij Azure.

```azurecli
az login
```

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. de app implementeren

1. **Uitvoeren** `az webapp up` in uw lokale map. **Vervang** <app-naam> door een globaal unieke naam.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Problemen oplossen</summary>
    <ul>
    <li>Als de <code>az</code> opdracht niet wordt herkend, moet u ervoor zorgen dat de Azure cli is geïnstalleerd, zoals beschreven in <a href="#1-prepare-your-environment">uw omgeving voorbereiden</a>.</li>
    <li>Vervang door <code>&lt;app-name&gt;</code> een unieke naam in alle Azure ( <em> geldige tekens zijn <code>a-z</code> , <code>0-9</code> en <code>-</code> </em> ). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.</li>
    <li>Met het argument <code>--sku F1</code> maakt u de web-app in de prijscategorie Gratis. Laat dit argument weg om een snellere Premium-laag te gebruiken, waarmee u kosten per uur in rekening worden gebracht.</li>
    <li>U kunt eventueel het argument <code>--location &lt;location-name&gt;</code> toevoegen, waarbij <code>&lt;location-name&gt;</code> een beschikbare Azure-regio is. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de opdracht uit te voeren <a href="/cli/azure/appservice#az-appservice-list-locations"> <code>az account list-locations</code> </a> .</li>
    </ul>
    </details>
    
1. Wacht tot de opdracht is voltooid. Het kan enkele minuten duren en eindigt met "u kunt de app starten op http:// &lt; app-name &gt; . azurewebsites.net".

    <details>
    <summary>Wat <code>az webapp up</code> gebeurt er?</summary>
    <p>Met de opdracht <code>az webapp up</code> worden de volgende acties uitgevoerd:</p>
    <ul>
    <li>Er wordt een standaardresourcegroep gemaakt.</li>
    <li>Maak een standaard App Service plan.</li>
    <li><a href="/cli/azure/webapp#az-webapp-create">Maak een app service-app</a> met de opgegeven naam.</li>
    <li>Er worden via zip bestanden van de huidige werkmap naar de app <a href="/azure/app-service/deploy-zip">geïmplementeerd</a>.</li>
    <li>Tijdens de uitvoering worden berichten over het maken van resources, logboek registratie en ZIP-implementatie geboden.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Voorbeelduitvoer van de opdracht az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Voorbeelduitvoer van de opdracht az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Blader naar de app

**Blader naar de geïmplementeerde toepassing** met behulp van uw webbrowser.

```bash
http://<app_name>.azurewebsites.net
```

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. de code bijwerken en opnieuw implementeren

1. **Open het bestand _Startup. cs_** in de lokale map. 

1. **Breng een kleine wijziging** aan in de tekst in de methode aanroep `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Sla uw wijzigingen** op.

1. **Uitvoeren** `az webapp up` opnieuw implementeren:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Wat <code>az webapp up</code> gebeurt er nu?</summary>
    De eerste keer dat u de opdracht uitvoert, worden de app-naam, de resource groep en het App Service plan opgeslagen in het <i>. Azure/config-</i> bestand van de hoofdmap van het project. Wanneer u het opnieuw uitvoert vanuit de hoofdmap van het project, worden de waarden gebruikt die zijn opgeslagen in <i>. Azure/config</i>, detecteert dat de app service resources al bestaan en voert de zip-implementatie opnieuw uit.
    </details>
    
1. Zodra de implementatie is voltooid, klikt u op **vernieuwen** in het browser venster dat eerder is geopend.

    ![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. uw nieuwe Azure-app beheren

1. Ga naar de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Schermopname van de App Services-pagina waarop een voorbeeld-Azure-app is geselecteerd.":::

1. Op de overzichts pagina kunt u basis beheer taken uitvoeren, zoals bladeren, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

    ![App Service-pagina in Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. opschonen van resources

**Uitvoeren** `az group delete --name myResourceGroup` de resource groep verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: ASP.NET Core-app met SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [ASP.NET Core-app configureren](configure-language-dotnetcore.md)

::: zone-end
