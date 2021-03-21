---
title: Application Insights verwijderen in Visual Studio-Azure Monitor
description: Application Insights SDK voor ASP.NET en ASP.NET Core verwijderen in Visual Studio.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1d70413fa6a47e2d41693db6eb705f31b2a2b1b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101704307"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Application Insights verwijderen in Visual Studio

In dit artikel wordt uitgelegd hoe u de ASP.NET en ASP.NET Core Application Insights SDK verwijdert in Visual Studio.

Als u Application Insights wilt verwijderen, moet u de NuGet-pakketten en-verwijzingen verwijderen uit de API in uw toepassing. U kunt NuGet-pakketten verwijderen met behulp van de Package Management-console of de NuGet-oplossing beheren in Visual Studio. In de volgende secties ziet u twee manieren om NuGet-pakketten te verwijderen en wat er automatisch aan het project is toegevoegd. Zorg ervoor dat u de toegevoegde bestanden en gebieden met in uw eigen code waarin u aanroepen naar de API hebt gemaakt, hebt bevestigd.

## <a name="uninstall-using-the-package-management-console"></a>Verwijderen met de pakket beheer console

# <a name="net"></a>[.NET](#tab/net)

1. Als u de Package Management-console wilt openen, selecteert u in het bovenste menu Hulpprogram Ma's > NuGet package manager > Package Manager-console.
     
    ![Klik in het menu aan de bovenkant op extra > NuGet package manager > Package Manager-console](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Als traceer verzameling is ingeschakeld, moet u eerst micro soft. ApplicationInsights. TraceListener verwijderen. Voer `Uninstall-package Microsoft.ApplicationInsights.TraceListener` vervolgens de volgende stap uit om micro soft. ApplicationInsights. web te verwijderen.

1. Voer de volgende opdracht in: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    Nadat u de opdracht hebt ingevoerd, worden het Application Insights-pakket en alle afhankelijkheden ervan verwijderd uit het project.
    
    ![Opdracht invoeren in console](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Als u de Package Management-console wilt openen, selecteert u in het bovenste menu Hulpprogram Ma's > NuGet package manager > Package Manager-console.

    ![Klik in het menu aan de bovenkant op extra > NuGet package manager > Package Manager-console](./media/remove-application-insights/package-manager.png)

1. Voer de volgende opdracht in: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    Nadat u de opdracht hebt ingevoerd, worden het Application Insights-pakket en alle afhankelijkheden ervan verwijderd uit het project.

---

## <a name="uninstall-using-the-visual-studio-nuget-ui"></a>Verwijderen met behulp van de NuGet-gebruikers interface van Visual Studio

# <a name="net"></a>[.NET](#tab/net)

1. Klik in de *Solution Explorer* aan de rechter kant op **oplossing** en selecteer **NuGet-pakketten beheren voor oplossing**.

    Vervolgens wordt er een scherm weer gegeven waarin u alle NuGet-pakketten kunt bewerken die deel uitmaken van het project.
    
     ![Klik met de rechter muisknop op de oplossing in het Solution Explorer en selecteer vervolgens NuGet-pakketten beheren voor oplossing](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Als traceer verzameling is ingeschakeld, moet u eerst micro soft. ApplicationInsights. TraceListener verwijderen zonder de afhankelijkheden te verwijderen en vervolgens de onderstaande stappen volgen om micro soft. ApplicationInsights. web te verwijderen met geselecteerde afhankelijkheden verwijderen.

1. Klik op het webpakket **micro soft. ApplicationInsights. Web** . Schakel aan de rechter kant het selectie vakje naast **project** in om alle projecten te selecteren.

1. Als u alle afhankelijkheden wilt verwijderen wanneer u de installatie ongedaan maakt, selecteert u de vervolg keuze knop **Opties** onder de sectie waarin u project hebt geselecteerd.

    Schakel onder *Opties voor verwijderen* het selectie vakje in naast *afhankelijkheden verwijderen*.

1. Selecteer **Verwijderen**.
    
    ![Scherm afbeelding toont het venster micro soft. ApplicationInsights. web met de optie voor het verwijderen van afhankelijkheden is geselecteerd en verwijderen gemarkeerd.](./media/remove-application-insights/uninstall-framework.png)

    Er verschijnt een dialoog venster waarin alle afhankelijkheden worden weer gegeven die uit de toepassing moeten worden verwijderd. Selecteer **OK** om te verwijderen.
    
    ![In de scherm afbeelding wordt een dialoog venster weer gegeven met de afhankelijkheden die moeten worden verwijderd.](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Nadat alles is verwijderd, ziet u mogelijk nog steeds ' ApplicationInsights.config ' en ' AiHandleErrorAttribute. cs ' in de *Solution Explorer*. U kunt de twee bestanden hand matig verwijderen.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. Klik in de *Solution Explorer* aan de rechter kant op **oplossing** en selecteer **NuGet-pakketten beheren voor oplossing**.

    Vervolgens wordt er een scherm weer gegeven waarin u alle NuGet-pakketten kunt bewerken die deel uitmaken van het project.

    ![Klik met de rechter muisknop op de oplossing in het Solution Explorer en selecteer vervolgens NuGet-pakketten beheren voor oplossing](./media/remove-application-insights/manage-nuget-core.png)

1. Klik op het pakket micro soft. ApplicationInsights. AspNetCore. Schakel aan de rechter kant het selectie vakje in naast *project* om alle projecten te selecteren en selecteer vervolgens **verwijderen**.

    ![Schakel afhankelijkheden verwijderen uit en verwijder vervolgens](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Wat wordt er gemaakt wanneer u Application Insights toevoegt

Wanneer u Application Insights toevoegt aan uw project, worden er bestanden gemaakt en code toegevoegd aan een aantal van uw bestanden. Als u de NuGet-pakketten verwijdert, worden de bestanden en code niet altijd verwijderd. Als u Application Insights volledig wilt verwijderen, moet u de toegevoegde code of bestanden samen met de API-aanroepen die u in uw project hebt toegevoegd, controleren en hand matig verwijderen.

# <a name="net"></a>[.NET](#tab/net)

Wanneer u Application Insights Telemetry toevoegt aan een Visual Studio ASP.NET-project, worden de volgende bestanden toegevoegd:

- ApplicationInsights.config
- AiHandleErrorAttribute. cs

De volgende stukjes code worden toegevoegd:

- [De naam van uw project]. csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Packages.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout. cshtml

    Als uw project een indeling. cshtml-bestand bevat, wordt de onderstaande code toegevoegd.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.jsop

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig. cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Wanneer u Application Insights Telemetry toevoegt aan een sjabloon project van Visual Studio ASP.NET Core, wordt de volgende code toegevoegd:

- [De naam van uw project]. csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.jsop:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.jsop
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>Volgende stappen

- [Azure Monitor](../overview.md)
