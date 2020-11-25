---
title: Profiel ASP.NET Core Azure Linux-web-apps met Application Insights Profiler | Microsoft Docs
description: Een conceptueel overzicht en stapsgewijze zelf studie over het gebruik van Application Insights Profiler.
ms.topic: conceptual
ms.custom: devx-track-csharp
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 69ec25348c3056536a2e09fd889b48e1e63ea7bb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998155"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profiel ASP.NET Core Azure Linux-web-apps met Application Insights Profiler

Deze functie is momenteel beschikbaar als preview-product.

Ontdek hoeveel tijd wordt besteed aan elke methode van uw Live Web-app wanneer u [Application Insights](./app-insights-overview.md)gebruikt. Application Insights Profiler is nu beschikbaar voor ASP.NET Core web-apps die worden gehost in Linux op Azure App Service. Deze hand leiding bevat stapsgewijze instructies voor het verzamelen van de profilerings traceringen voor ASP.NET Core Linux-web-apps.

Nadat u deze procedure hebt voltooid, kan uw app profilerings traceringen verzamelen zoals de traceringen die in de installatie kopie worden weer gegeven. In dit voor beeld geeft de profilerings tracering aan dat een bepaalde webaanvraag langzaam is omdat er wacht tijd wordt besteed. Het *warme pad* in de code waarmee de app wordt vertraagd, is gemarkeerd met een vlam pictogram. De methode **about** in het gedeelte **HomeController** vertraagt de web-app omdat de methode de **thread. slaapstandmodus** oproept.

![Profiler traceringen](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Vereisten
De volgende instructies zijn van toepassing op alle ontwikkel omgevingen van Windows, Linux en Mac:

* Installeer [.net core SDK 2.1.2 of hoger](https://dotnet.microsoft.com/download/archives).
* Installeer Git door de instructies in aan de slag [-git installeren](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)te volgen.

## <a name="set-up-the-project-locally"></a>Het project lokaal instellen

1. Open een opdracht prompt venster op de computer. De volgende instructies werken voor alle ontwikkel omgevingen van Windows, Linux en Mac.

1. Maak een ASP.NET Core MVC-webtoepassing:

   ```console
   dotnet new mvc -n LinuxProfilerTest
   ```

1. Wijzig de werkmap in de hoofdmap voor het project.

1. Voeg het NuGet-pakket toe voor het verzamelen van de profilerings traceringen:

   ```console
   dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
   ```

1. Application Insights in Program.cs inschakelen:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```

1. Profiler inschakelen in Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Voeg in de sectie **HomeController.cs** een regel code toe om enkele seconden een wille keurige vertraging uit te stellen:

    ```csharp
    using System.Threading;
    ...

    public IActionResult About()
        {
            Random r = new Random();
            int delay = r.Next(5000, 10000);
            Thread.Sleep(delay);
            return View();
        }
    ```

1. Sla uw wijzigingen op in de lokale opslag plaats en voer deze in:

    ```console
    git init
    git add .
    git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>De Linux-web-app maken om uw project te hosten

1. Maak de web app-omgeving met behulp van App Service op Linux:

    ![De Linux-web-app maken](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. De implementatie referenties maken:

    > [!NOTE]
    > Neem uw wacht woord op om het later te gebruiken bij het implementeren van uw web-app.

    ![De implementatie referenties maken](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Kies de implementatie opties. Stel een lokale Git-opslag plaats in de web-app in met behulp van de instructies op de Azure Portal. Er wordt automatisch een Git-opslag plaats gemaakt.

    ![De Git-opslag plaats instellen](./media/profiler-aspnetcore-linux/setup-git-repo.png)

Zie [app service-documentatie](../../app-service/index.yml)voor meer implementatie opties.

## <a name="deploy-your-project"></a>Uw project implementeren

1. Blader in het opdracht prompt venster naar de hoofdmap van uw project. Voeg een Git externe opslag plaats toe om te verwijzen naar de opslag plaats op App Service:

    ```console
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Gebruik de **gebruikers naam** die u hebt gebruikt voor het maken van de implementatie referenties.
    * Gebruik de **naam** van de app die u hebt gebruikt om de web-app te maken met behulp van app service op Linux.

2. Implementeer het project door de wijzigingen naar Azure te pushen:

    ```console
    git push azure master
    ```

    De uitvoer ziet er ongeveer als volgt uit:

    ```output
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    ...
    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Application Insights toevoegen om uw web-apps te bewaken

1. [Een Application Insights-resource maken](./create-new-resource.md).

2. Kopieer de waarde **iKey** van de resource Application Insights en stel de volgende instellingen in uw web-apps in:

    `APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]`

    Wanneer de app-instellingen worden gewijzigd, wordt de site automatisch opnieuw opgestart. Nadat de nieuwe instellingen zijn toegepast, wordt de Profiler onmiddellijk twee minuten uitgevoerd. De Profiler wordt vervolgens elk uur twee minuten uitgevoerd.

3. Genereer een deel van het verkeer naar uw website. U kunt verkeer genereren door de site **over** een paar keer te vernieuwen.

4. Wacht twee tot vijf minuten totdat de gebeurtenissen worden geaggregeerd naar Application Insights.

5. Blader naar het deel venster Application Insights **prestaties** in de Azure Portal. U kunt de Profiler-traceringen weer geven rechtsonder in het deel venster.

    ![Profiler traceringen weer geven](./media/profiler-aspnetcore-linux/view-traces.png)



## <a name="next-steps"></a>Volgende stappen
Als u aangepaste containers gebruikt die worden gehost door Azure App Service, volgt u de instructies in [ enable service Profiler for a ASP.net core toepassing in een container](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) om Application Insights Profiler in te scha kelen.

Meld eventuele problemen of suggesties voor de Application Insights GitHub-opslag plaats: [ApplicationInsights-Profiler-AspNetCore: issues](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).