---
title: 'Quickstart: Een C# ASP.NET Core-app maken'
description: Leer hoe u web-apps uitvoert in Azure App Service door uw eerste ASP.NET Core-app te implementeren.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff86bedf47395b50dc25e552b8b3ed4176e23b65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769099"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Quickstart: Een ASP.NET Core-web-app maken in Azure

::: zone pivot="platform-windows"  

In deze quickstart leert u hoe u uw eerste ASP.NET Core-web-app maakt en implementeert in <abbr title="Een OP HTTP gebaseerde service voor het hosten van webtoepassingen, REST API's en mobiele back-endtoepassingen.">Azure App Service</abbr>. App Service ondersteunt .NET 5.0-apps.

Wanneer u klaar bent, hebt u een Azure-account <abbr title="Een logische container voor gerelateerde Azure-resources die u als eenheid kunt beheren.">resourcegroep</abbr>, bestaande uit een <abbr title="Het plan waarmee de locatie, grootte en functies van de webserverfarm worden opgegeven die als host voor uw app wordt gebruikt.">App Service-plan</abbr> en een <abbr title="De weergave van uw web-app, die uw app-code, DNS-hostnamen, certificaten en gerelateerde resources bevat.">App Service-app</abbr> met een geïmplementeerd voorbeeld van ASP.NET Core-toepassing.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Uw omgeving voorbereiden

- **Een Azure-account met** een actief account krijgen <abbr title="De basisstructuur van de organisatie waarin u resources in Azure beheert, meestal gekoppeld aan een individu of afdeling binnen een organisatie.">abonnement</abbr>. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- **Installeer <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>** met de **workload ASP.NET webontwikkeling.**

<details>
<summary>Hebt u Visual Studio 2019 al?</summary>
Als u Visual Studio 2019 al hebt geïnstalleerd:

<ul>
<li><strong>Installeer de nieuwste updates</strong> in Visual Studio door <strong>Help Controleren</strong> op updates te &gt; <strong>selecteren.</strong> De meest recente updates bevatten de .NET 5,0 SDK.</li>
<li><strong>Voeg de workload toe</strong> door <strong>Hulpprogramma's</strong> &gt; <strong>hulpprogramma's en functies te selecteren.</strong></li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. Een ASP.NET Core-web-app maken

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

## <a name="3-publish-your-web-app"></a>3. Uw web-app publiceren

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

1. Voer **bij** Naam een unieke app-naam in.

    <details>
        <summary>Welke tekens kan ik gebruiken?</summary>
        Geldige tekens zijn a-z, A-Z, 0-9 en -. U kunt de automatisch gegenereerde unieke naam accepteren. De URL van de web-app is http:// <code>&lt;app-name&gt;.azurewebsites.net</code> , waarbij de naam van uw app <code>&lt;app-name&gt;</code> is.
    </details>

1. Selecteer **Maken** om de Azure-resources te maken. 

   ![App-resources maken](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Wacht tot de wizard klaar is met het maken van Azure-resources. Klik op **Voltooien** om de wizard te sluiten.

1. Klik op **de pagina** Publiceren op **Publiceren om** uw project te implementeren. 

    <details>
        <summary>Wat is Visual Studio aan het doen?</summary>
        Visual Studio maakt, verpakt en publiceert de app naar Azure. Daarna wordt de app gestart in de standaardbrowser.
    </details>

   ![Gepubliceerde ASP.NET-web-app, uitgevoerd in Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. De app bijwerken en opnieuw installeren

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

## <a name="5-manage-the-azure-app"></a>5. De Azure-app beheren

1. Ga naar de [Azure Portal](https://portal.azure.com)en zoek en selecteer **App Services**.

    ![App Services selecteren](./media/quickstart-dotnetcore/app-services.png)
    
1. Selecteer op de pagina **App Services** de naam van uw web-app.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Schermopname van de App Services-pagina waarop een voorbeeldweb-app is geselecteerd.":::

1. De **Overzichtspagina** voor de web-app bevat opties voor basisbeheer, zoals browsen, stoppen, starten, opnieuw starten en verwijderen. Het linkermenu bevat een meer pagina's voor het configureren van uw app.

    ![App Service in de Azure-portal](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Resources ops schonen

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
In deze quickstart ziet u hoe u een [.NET Core-app](/aspnet/core/) maakt op <abbr title="App Service onder Linux biedt een uiterst schaalbare webhostingservice met self-patchfunctie onder het Linux-besturingssysteem.">App Service op Linux</abbr>. U maakt de app via de [Azure CLI](/cli/azure/get-started-with-azure-cli) en gebruikt Git om de .NET Core-code in de app te implementeren.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Uw omgeving voorbereiden

- **Haal een Azure-account** op met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/dotnet/)
- **Installeer** de nieuwste <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">.NET Core 3.1 SDK</a> of <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">.NET 5.0 SDK.</a>
- **<a href="/cli/azure/install-azure-cli" target="_blank">Installeer de nieuwste Versie van Azure CLI.</a>**

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. De app lokaal maken

1. Voer uit `mkdir hellodotnetcore` om de map te maken.

    ```bash
    mkdir hellodotnetcore
    ```

1. Voer uit `cd hellodotnetcore` om de map te wijzigen. 

    ```bash
    cd hellodotnetcore
    ```

1. Voer `dotnet new web` uit om een nieuwe .NET Core-app te maken.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. De app lokaal uitvoeren

1. Voer `dotnet run` uit om te zien hoe het eruitziet wanneer u het implementeert in Azure.

    ```bash
    dotnet run
    ```
    
1. **Open een webbrowser en** navigeer naar de app op `http://localhost:5000` .

![Testen met browser](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Aanmelden bij Azure

Voer `az login` uit om u aan te melden bij Azure.

```azurecli
az login
```

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. De app implementeren

1. **Uitvoeren** `az webapp up` in uw lokale map. **Vervang** <naam van de app> door een wereldwijd unieke naam.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Problemen oplossen</summary>
    <ul>
    <li>Als de opdracht niet wordt herkend, moet u ervoor zorgen dat de Azure CLI is geïnstalleerd zoals <code>az</code> beschreven in <a href="#1-prepare-your-environment">Uw omgeving voorbereiden.</a></li>
    <li>Vervang <code>&lt;app-name&gt;</code> door een naam die uniek is in Azure ( geldige tekens zijn , en <em> <code>a-z</code> <code>0-9</code> <code>-</code> </em> ). Het is handig om een combinatie van uw bedrijfsnaam en een app-id te gebruiken.</li>
    <li>Met het argument <code>--sku F1</code> maakt u de web-app in de prijscategorie Gratis. Laat dit argument weg om een snellere Premium-laag te gebruiken, waarmee u kosten per uur in rekening worden gebracht.</li>
    <li>U kunt eventueel het argument <code>--location &lt;location-name&gt;</code> toevoegen, waarbij <code>&lt;location-name&gt;</code> een beschikbare Azure-regio is. U kunt een lijst met toegestane regio's voor uw Azure-account ophalen door de opdracht uit te <a href="/cli/azure/appservice#az_appservice_list_locations"> <code>az account list-locations</code> </a> voeren.</li>
    </ul>
    </details>
    
1. Wacht tot de opdracht is voltooid. Dit kan enkele minuten duren en eindigt met 'U kunt de app starten op http:// &lt; app-naam &gt; .azurewebsites.net'.

    <details>
    <summary>Wat doet <code>az webapp up</code> u?</summary>
    <p>Met de opdracht <code>az webapp up</code> worden de volgende acties uitgevoerd:</p>
    <ul>
    <li>Er wordt een standaardresourcegroep gemaakt.</li>
    <li>Een standaardplan App Service maken.</li>
    <li><a href="/cli/azure/webapp#az_webapp_create">Maak een App Service app</a> met de opgegeven naam.</li>
    <li>Er worden via zip bestanden van de huidige werkmap naar de app <a href="/azure/app-service/deploy-zip">geïmplementeerd</a>.</li>
    <li>Tijdens het uitvoeren worden berichten over het maken van resources, logboekregistratie en ZIP-implementatie verstrekt.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Voorbeelduitvoer van de opdracht az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Voorbeelduitvoer van de opdracht az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Bladeren naar de app

**Blader naar de geïmplementeerde toepassing met** behulp van uw webbrowser.

```bash
http://<app_name>.azurewebsites.net
```

![Voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code&quot;></a>7. De code bijwerken en opnieuw uitvoeren

1. **Open het _bestand Startup.cs_** in de lokale map. 

1. **Maak een kleine wijziging in** de tekst in de methode -aanroep `context.Response.WriteAsync` .

    ```csharp
    await context.Response.WriteAsync(&quot;Hello Azure!");
    ```
    
1. **Sla uw wijzigingen op.**

1. **Uitvoeren** `az webapp up` om opnieuw te wordenploy:

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Wat gebeurt <code>az webapp up</code> er nu?</summary>
    De eerste keer dat u de opdracht hebt uitgevoerd, worden de naam van de app, de resourcegroep en het App Service opgeslagen in het <i>.azure/config-bestand</i> uit de hoofdmap van het project. Wanneer u deze opnieuw uitvoert vanuit de hoofdmap van het project, worden de waarden gebruikt die zijn opgeslagen in <i>.azure/config,</i>wordt gedetecteerd dat de App Service-resources al bestaan en wordt zip-implementatie opnieuw uitgevoerd.
    </details>
    
1. Nadat de implementatie is voltooid, **kunt u in het** browservenster dat eerder is geopend op Vernieuwen.

    ![Bijgewerkte voorbeeld-app die wordt uitgevoerd in Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Uw nieuwe Azure-app beheren

1. Ga naar de <a href="https://portal.azure.com" target="_blank">Azure Portal</a>.

1. Klik in het linkermenu op **App Services** en klik op de naam van uw Azure-app.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Schermopname van de App Services-pagina waarop een voorbeeld-Azure-app is geselecteerd.":::

1. Op de pagina Overzicht kunt u algemene beheertaken uitvoeren, zoals bladeren, stoppen, starten, opnieuw opstarten en verwijderen. Het linkermenu bevat een aantal pagina's voor het configureren van uw app. 

    ![App Service-pagina in Azure Portal](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Resources ops schonen

**Uitvoeren** `az group delete --name myResourceGroup` om de resourcegroep te verwijderen.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Ondervindt u problemen? Laat het ons weten.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: ASP.NET Core-app met SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [ASP.NET Core-app configureren](configure-language-dotnetcore.md)

::: zone-end
